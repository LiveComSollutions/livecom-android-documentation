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

You must upload Instant App in separate release type in Play Store. In order to create this type go to:
Play console -> Tap your application -> Setup -> Advanced Settings -> Release Types -> Add Release Type -> Google Play Instant
<img width="1305" alt="Play_Store_release" src="https://user-images.githubusercontent.com/102226507/171581841-9052d8b1-1352-4294-8f81-012d6a6974eb.png">

It takes up to three days after all conditions fulfilled Google robot to scan `assetlinks.json` file on your web domain. You can read about `assetlinks.json` from links in point 4 above. Also at this period Google finds that we have Instant App and “Try now” button appears on our application page in Play Store. Direct app links to our application (even if it not installed) should start to work as soon as this button appears.  

If “Try now“ do not appear you need to check whether Instant app enabled for your Google account. Steps are:
1. Open Play Market
2. Open settings (tap on avatar usually)
3. Open “General” section
4. Open “Google Play Instant”
5. Switch on “Upgrade web links”

# How to connect Instant App with LiveCom SDK

1. Implement Instant App settings described above.
2. Integrate LiveCom SDK as written [here](https://github.com/LiveComSollutions/android-sdk-documentation)

Be sure to sync your App Links with other platforms (for example iOS).
Let's look at example:  
1. We have links like `https://thesollution.com/s/{videoId}`
2. `assetlinks.json` should look like https://thesollution.com/.well-known/assetlinks.json. Paste in it your application id and your SHA-256 fingerprints. Don't forget about Google Play Store signing key. It must be inserted here too.
3. In your Activity, that starts on App Link fetch, parse link and extract video id:
```kotlin
class MyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val link = intent.data ?: return // there is no link in intent, do what you need
        
        // https://thesollution.com/s/{videoId}
        
        val pathSegments = link.pathSegments
        if (pathSegments.isEmpty()) {
            // there is no link in intent, do what you need
            lifecycleScope.launchWhenCreated {
                  LiveCom.openVideoListScreen(this@MyActivity)
                  finish()
            }
            return
        }

        val id = pathSegments.getOrNull(1) ?: kotlin.run {
            // there is no link in intent, do what you need
            lifecycleScope.launchWhenCreated {
                  LiveCom.openVideoListScreen(this@MyActivity)
                  finish()
            }
            return
        }

        if (pathSegments[0] == "s") {
            lifecycleScope.launchWhenCreated {
                LiveCom.openVideoById(this@MyActivity, streamId = id)
                finish()
            }
        } else {
            // there is no link in intent, do what you need
            lifecycleScope.launchWhenCreated {
                  LiveCom.openVideoListScreen(this@MyActivity)
                  finish()
            }
        }
    }
}
```
