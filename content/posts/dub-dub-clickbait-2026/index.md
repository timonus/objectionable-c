+++
date = '2026-05-25T05:00:00-07:00'
title = 'Dub Dub Clickbait 2026'
+++

This is the least connected I’ve felt to WWDC in years, in fact I booked a flight during the keynote 🙃. That’s not to say my love for Apple has waned, I think maybe the dud that was Apple Intelligence last year just has me less tuned-in as most years. Nonetheless, I’m excited to see what they announce.

There’s always shiny new features during dub dub, but what I really love is quality-of-life refinements to mature APIs that I’ve used for ages. The canonical example I use is when iOS 15 introduced a [dedicated image decoding API](https://developer.apple.com/documentation/uikit/uiimage?changes=latest_minor&language=objc#Loading-images-for-display), or iOS 13’s introduction of [first-party animated GIF playback](https://github.com/timonus/UIImageViewAnimatedGIF). APIs that make you go “finally!”

A couple years ago I started recording little API wishes I had for Apple to put together a sort of “bingo card” for future WWDCs. I feel like publishing it is a little clickbait-y, but might as well in case it resonates with folks or developers at Apple might see it. Here’s what I’d love to see in no particular order:

- Add a built in hex-to-`UIColor` method
- Add a “Toast” UI component
- Add images to actions in `UIAlertController`
- Extended swipe to pop navigation controller gestures to full screen easily (like [this](https://github.com/timonus/TJExtendedNavigationPop))
- Aesthetic text wrapping (like [this](https://github.com/kylebshr/AestheticText))
- Easily detect if text in `UILabel`/`UITextView` is truncated, provide alternates to fit
- Tappable links in `UILabel` (like [this](https://github.com/timonus/UILabelTapHandling))
- Better share/action extension disabling rules
- Auto-sizing `UISheetPresentationController`
- Nav bar header customization via appearance proxy
- Open up translate sheet to UIKit (like [this](https://x.com/SebJVidal/status/1801887531048349749))
- More fine grained control of `UIVisualEffectView`
- Get size of keyboard arbitrarily, without it even being shown
- Expose `NSDataCompressionAlgorithmBrotli` ([this](https://objectionable-c.com/posts/brotli-ios/#:~:text=Hopefully%20Apple%20makes%20this%20enum%20value%20public%20in%20the%20future!%20(FB16918276)))
- A better way of creating launch screens (maybe more control over [`UILaunchScreen`](https://sarunw.com/posts/launch-screen-using-plist/))
- Make it so wireless debugging can be turned off
- Allow typing `:emoji-name:` to autocomplete emojis in text fields
- Allow double tap on hardware action button to trigger a specific action
- Add “Unsubscribe” action to email notifications