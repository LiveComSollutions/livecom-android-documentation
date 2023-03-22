## LiveCom methods and parameters
### LiveCom.configure()

This method remembers your sdk token and domain to generate a share link. Also, it downloads localization, images and some other configurations from sdk api in order to work correctly.

Parameters:

`applicationContext: Context` - Android application context;

`sdkToken: String` - your sdk token, received from company manager;

`shareDomain: String` - domain that will be used to generate links like: `https://$shareDomain/$videoId`. Share feature available on player screen and product screen;

### LiveCom.openSdkScreen()

Opens screen depending on `entranceCommand` argument. Technically, it starts new activity, that will manage internal sdk fragments navigation. Start screen can be video list, full screen player or checkout. Note that this method may suspend thread if sdk configuration loading is not completed yet.

Parameters:

`entranceCommand: SdkEntrance` - sealed class that tells what to open.

You can open the sdk screen in three ways:

1.  Open video list screen. Use `SdkEntrance.OpenVideoList`.
2.  Open video player with some video id. Use `SdkEntrance.OpenVideo` with correct `OpenVideo.streamId`. Optionally you can pass `OpenVideo.productId`. If you pass `productId` and video with `streamId` contains this product, then the product card will be opened automatically.
3.  Open checkout screen. Use `SdkEntrance.OpenCheckout`

For example, if you want to open video list as first screen, and video player with the exact player as second screen immediately. You can call two commands sequentially:

```kotlin
LiveCom.openSdkScreen(
  SdkEntrance.OpenVideoList,
  activity
)
LiveCom.openSdkScreen(
  SdkEntrance.OpenVideo(streamId),
  activity
)
```
