+++
date = '2025-05-16T05:00:00-07:00'
title = 'No-nonsense UISceneDelegate migration reference'
+++

![](meme.jpg)

I've avoided migrating to using the `UIScene`-based application lifecycle since it was announced with iOS 13 because:
- None of my apps support multiple windows (the main reason for `UIScene` to exist afaict)
- I preferred the older, simpler methods of dealing with app events via `UIApplicationDelegate`.

I've also seen numerous apps break in weird ways due to differences in behavior between `UIApplicationDelegate` and `UISceneDelegate`. Most notably, several events that get their own methods in `UISceneDelegate` are handled completely differently on cold start when a scene is being spun up. This often causes [URL handling](https://mastodon.social/@timonus/114433444611806352) and shortcut handling to break on cold start. Another common source of issues is that app delegate lifecycle methods (`didBecomeActive`/`didEnterBackground`/etc.) are no longer called, even though the notifications for those events are still fired 🤔.

It seems that [as of iOS 18.4](https://developer.apple.com/documentation/technotes/tn3187-migrating-to-the-uikit-scene-based-life-cycle) Apple is starting to force developers towards using the `UIScene`-based lifecycle, so I took a stab at porting my apps over.

I've created a project named [`TJSceneDelegate`](https://github.com/timonus/tjscenedelegate) that should more or less acts as a drop in replacement in any app. When installed it forwards all events to `UIApplicationDelegate`, the thought is that you should then be able to forget about scenes and use the app delegate callbacks like you used to. At the moment I've ported all but one of my side projects over to this, and I'm going to migrate the final one soon.

I hope this can serve as a solution or a reference for others migrating to the `UIScene`-based lifecycle.