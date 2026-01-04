+++
date = '2025-03-24T06:00:00-07:00'
title = 'gzip yer uploads'
subtitle = 'Compression is a two-way street'
+++

`NSURLSession` opaquely handles HTTP negotiations about accepting compressed content and does the decompression (using formats like gzip and [brotli](/posts/brotli-ios)) before you receive responses, which is great. `Foundation`, Apple's OSes, and the web are doing work that we as developers don't have to so our users consume less data and have a faster experience. Hooray! I've always been impressed that this happens pretty much auto-magically without having to think about it.

However, I had a thought late in 2024 about transmitting data the other way: uploads. While looking I realized there's not automatic upload compression built into `NSURLSession`. I'm sure there are good reasons for this, but I figured that some of the APIs I was using could accept compressed uploads. It turns out I was right, [Mixpanel](https://developer.mixpanel.com/reference/import-events) and Dropbox both accept compressed data.

Here's how I'm doing the compression itself

```objc
#import <zlib.h>

// Function that gzips data
// Thanks Claude https://tijo.link/BGdVNx
static NSData *_gzipCompressData(NSData *const data, NSError **error) {
   if (!data.length) {
       if (error) {
           *error = [NSError errorWithDomain:@"CompressionErrorDomain" code:1
               userInfo:@{NSLocalizedDescriptionKey: @"Invalid input data"}];
       }
       return nil;
   }
   
   z_stream strm;
   strm.zalloc = Z_NULL;
   strm.zfree = Z_NULL;
   strm.opaque = Z_NULL;
   
   // Initialize deflate with gzip format
   if (deflateInit2(&strm, Z_DEFAULT_COMPRESSION, Z_DEFLATED, 15 + 16, 8, Z_DEFAULT_STRATEGY) != Z_OK) {
       if (error) {
           *error = [NSError errorWithDomain:@"CompressionErrorDomain" code:2
               userInfo:@{NSLocalizedDescriptionKey: @"Failed to initialize compression"}];
       }
       return nil;
   }
   
   // Set up input
   strm.avail_in = (uInt)data.length;
   strm.next_in = (Bytef *)data.bytes;
   
   // Prepare output buffer (compress can increase size)
   NSMutableData *compressedData = [NSMutableData dataWithLength:data.length * 1.1 + 12];
   strm.avail_out = (uInt)compressedData.length;
   strm.next_out = compressedData.mutableBytes;
   
   // Compress
   if (deflate(&strm, Z_FINISH) != Z_STREAM_END) {
       deflateEnd(&strm);
       if (error) {
           *error = [NSError errorWithDomain:@"CompressionErrorDomain" code:3
               userInfo:@{NSLocalizedDescriptionKey: @"Compression failed"}];
       }
       return nil;
   }
   
   // Cleanup and finalize
   [compressedData setLength:strm.total_out];
   deflateEnd(&strm);
   
   return compressedData;
}
```

(You could also use a library like [this one](https://github.com/nicklockwood/GZIP) if you don't want to hand write the compression.)

Then, if using a data task:

```objc
NSData *compressedData = _gzipCompressData(originalData, &err);
NSURLRequest *request = ...;
[request setValue:@"gzip" forHTTPHeaderField:@"Content-Encoding"];
[request setHTTPBody:compressedData];
// Start a data task using request and an NSURLSession
```

or an upload task:

```objc
NSData *compressedData = _gzipCompressData(originalData, &err);
NSURLRequest *request = ...;
[request setValue:@"gzip" forHTTPHeaderField:@"Content-Encoding"];
[urlSession uploadTaskWithRequest:request fromData:compressedData];
```

With this you can shave a few percent off your upload sizes saving bandwidth and time!

*Here are examples where I'm doing this in [`TJMixpanelLogger`](https://github.com/timonus/TJMixpanelLogger/blob/b0ba0e704c11a97410a459f7b31d705caa335c6e/TJMixpanelLogger.m#L314-L323) and [`TJDropbox`](https://github.com/timonus/TJDropbox/blob/a35deac894646d5468ac06c645a8451c680d5962/TJDropbox/TJDropbox.m#L975-L986).*