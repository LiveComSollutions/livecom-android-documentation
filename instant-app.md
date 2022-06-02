# How to integrate instant app

Actually instant app integration process is fully described in [official documentation](https://developer.android.com/topic/google-play-instant/overview). This document describe only main instant app integration flow. If you see some undesirable behavior, or something does not work, please refer to official docs.  

We need to obey several conditions in order Instant App could work:

1. Regular android application must be published (not Instant App). Publishing to Play Store testing channel is enough.
2. Instant App `versionCode` must be less than regular android application `versionCode`. You must take care about codes reserve.
3. Package id for regular and Instant applications must be the same
4. [App Links](https://developer.android.com/training/app-links) for both applications must be implemented (also [guide for app links](https://developer.android.google.cn/studio/write/app-link-indexing?hl=en)).
5. At least one default url must present in `AndroidManifest.xml` inside activity tag.

```
<meta-data
      android:name="default-url"
      android:value="https://your_url.here" />
```

It takes up to three days after all conditions fulfilled Google robot to scan `assetlinks.json` file on your web domain. You can read about `assetlinks.json` from links in point 4 above. Also at this period Google finds that we have Instant App and “Try now” button appears on our application page in Play Store. Direct app links to our application (even if it not installed) should start to work as soon as this button appears.  

If “Try now“ do not appear you need to check whether Instant app enabled for your Google account. Steps are:
1. Open Play Market
2. Open settings (tap on avatar usually)
3. Open “General” section
4. Open “Google Play Instant”
5. Switch on “Upgrade web links”

# How to connect Instant App with LiveCom SDK
