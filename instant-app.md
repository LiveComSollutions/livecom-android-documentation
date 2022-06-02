# How to integrate instant app

Actually how to integrate instant app is fully described in [official documentation](https://developer.android.com/topic/google-play-instant/overview). This document describe only main instant app integration flow. If you see some undesirable behavior, or something does not work, please refer to official docs.  

We need to obey several conditions in order Instant App could work:

1. Regular android application must be published (not Instant App). Publishing to Play Market testing channel is enough.
2. Instant App `versionCode` must be less than regular android application `versionCode`. You must take care about codes reserve.
3. Package id for regular and Instant applications must be the same
4. [App Links](https://developer.android.com/training/app-links) for both applications must be implemented (also [guide for app links](https://developer.android.google.cn/studio/write/app-link-indexing?hl=en)).
5. At least one default url must present in `AndroidManifest.xml` inside activity tag.
