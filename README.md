### Warning

All code samples in this document presented using kotlin language (kotlin DSL for gradle scripts).

1. [How to install](#how-to-install)
2. [LiveCom methods and parameters](#livecom-methods-and-parameters)
3. [SDK exit points](#sdk-exit-points)
4. [Caveats](#caveats)
5. [Google Maps SDK integration](#google-maps-sdk-integration)

# How to install

1. In your android project `settings.gradle` file add repository <https://nexus.livecom.tech/repository/maven-android/>:

   ```kotlin
   dependencyResolutionManagement {
       repositories {
           ...
           maven("https://nexus.livecom.tech/repository/maven-android/")
       }
   }
   ```
2. Inside your module (where you want to add sdk dependency) `build.gradle` file add:

   ```kotlin
   dependencies {
       ....
       implementation("com.livecommerceservice:sdk:{latest_version}")
   }
   ```
3. In your project make call:

   ```kotlin
   LiveCom.configure(
      appContext: Context,
      sdkToken: String,
      shareDomain: String
   )
   ```

   Call this method as soon as possible. Because it needs time to loads some sdk configuration from network. Methods and parameters described [further](#livecom-methods-and-parameters)
4. To open sdk start screen call:

   ```kotlin
   LiveCom.openSdkScreen(entranceCommand: SdkEntrance)
   ```
   More about this method read below.

# LiveCom methods and parameters

### `LiveCom.configure()`

This method remembers your sdk token and domain to generate share link. Also it downloads localization, images and some other configuration from sdk api in order to work correctly.

Parameters:

* `applicationContext: Context` - Android application context;
* `sdkToken: String` - your sdk token, received from company manager;
* `shareDomain: String` - domain that will be used to generate links like: `https://$shareDomain/s/$videoId`. Share feature available on player screen and product screen;

### `LiveCom.openSdkScreen()`

Opens screen depending on `entranceCommand` argument. Technically it starts new activity, that will manage internal sdk fragments navigation. Start screen can be video list or full screen player. Note that this method may suspend thread if sdk configuration loading is not completed yet.

Parameters:

* `entranceCommand: SdkEntrance` - sealed class that tells what to open. 

You can open sdk screen in three ways:
   
1. Open video list screen and open video player above video list immediate.
2. Just open video list without video player
3. Open video player without video list. In this case if you will navigate back on video player
screen, you will exit sdk and will not see video list screen.  

In order to open point 1 - use `SdkEntrance.OpenVideo` with correct `SdkEntrance.OpenVideo.streamId` and `SdkEntrance.OpenVideo.shouldOpenVideoList == true`. 
In order to open point 2 - use `SdkEntrance.OpenVideoList`. 
In order to open point 3 - use `SdkEntrance.OpenVideo` with correct `SdkEntrance.OpenVideo.streamId` and `SdkEntrance.OpenVideo.shouldOpenVideoList == false`. 

Keep in mind, that if you entered sdk with `SdkEntrance.OpenVideo.shouldOpenVideoList == false`, you can not
open video list screen. If you will call `SdkEntrance.OpenVideoList` after `SdkEntrance.OpenVideo` with `shouldOpenVideoList == false`
nothing will happen.
Also if you enter sdk with `SdkEntrance.OpenVideoList` and invoke `SdkEntrance.OpenVideo` after it, then `shouldOpenVideoList` will be
ignored.
Conclusion: you can change sdk entrance type only after you finish previous sdk session (e.g. close all screens and open it from scratch)

# SDK exit points.

SDK contains several screens for user. Player, Product screen, Checkout and ThankYou page. They all can be reached by user by default. But if developer wants to show his own Product and/or Checkout screens, he has such possibility. `LiveCom` singleton has public var field `callback`. You can implement `LiveCom.Callback` interface and set this implementation to `LiveCom.callback` variable.

This is how Callback interface declared in SDK:

```kotlin
object LiveCom {
   
   var callback: Callback = object : Callback {
        override fun openProductCardInsideSdk(productId: String): Boolean = true

        override fun openCheckoutInsideSdk(productsInCart: List<LiveComProductInCart>): Boolean =
            true
    }

   interface Callback {
        /**
         * @param productId id of product that was clicked and should be opened on separate screen
         * @return
         * - ***true*** - if sdk should open its own product screen.
         * - ***false*** - if you want to show your product screen instead of sdks built in one.
         */
        fun openProductCardInsideSdk(productId: String): Boolean

        /**
         * @param productsInCart product ids, selected parameters and quantity that user added to cart
         * @return
         * - ***true*** - if sdk should open its own checkout screen.
         * - ***false*** - if you want to show your checkout screen instead of sdks built in one.
         */
        fun openCheckoutInsideSdk(productsInCart: List<LiveComProductInCart>): Boolean
    }
}
```

Default `callback` implementation always returns `true` from both methods. This means that internal SDK Product and Checkout screens will be opened when needed. If your implementation of this methods returns `false` (from one or both methods), then corresponding screen will not be shown. And it's your responsibility to show some screen.

Both methods has arguments.

1. `openProductCardInsideSdk` has `productId`. This is id of product that we want to open on Product screen.
2. `openCheckoutInsideSdk` has `productsInCart` list of `LiveComProductInCart` data objects. This object contains
   * `productId`
   * `count` - quantity of this product modification, that user added to cart.
   * `selectedParams` - list of selected product parameter id and selected item id of this parameter.

# Caveats

According to [Android Application lifecycle](https://developer.android.com/guide/components/activities/process-lifecycle) any process can be killed by the system. All static variables and other state will be lost when this happens. Developer can save some primitives, Serializable and Parcelable objects in Bundle. But `LiveCom.callback` is not one of them. That is why it can not be serialized. And that is why this variable separated from `LiveCom.configure()` method. It's sdk user (you - whoever is reading this now) responsibility to set this callback again after application recreated by system. Otherwise you will lose control over SDK navigation flow.

# Google Maps SDK integration

LiveCommerce SDK has Google Maps dependency. In order to open maps you need to select “Pickup” delivery type on checkout screen. Pickup bottom sheet appears and contains “Map” button. But if you want this button to be visible, в файле you should include such block in `AndroidManifest.xml`:
```
<meta-data
     android:name="com.google.android.geo.API_KEY"
     android:value="${MAPS_API_KEY}" />
```
Replace MAPS_API_KEY with key you received in Google Cloud Console (GCC) for Google Maps. How to get this key is written [here](https://developers.google.com/maps/documentation/android-sdk/get-api-key).  

Api key could leek into Git if you will insert it directly into `AndroidManifest.xml`. This is not “true” way. Integration apps can use GitHub - [google/secrets-gradle-plugin: A Gradle plugin for providing your secrets to your Android project](https://github.com/google/secrets-gradle-plugin). to avoid this. We use this in our demo application (Makeupus).  
Inside pickup bottom sheet we check if manifest contains correct block with meta-data. Map button becomes visible if correct meta-data exists. Button is hidden if correct meta-data not found.  
Developer who integrates LiveCom sdk is responsible for this meta-data block in his application. If meta-data block is not added, then everything will work without Google Maps.

# Style customization
SDK allows to customize some values. They are:
1. Fonts. Three custom fonts could be provided - Regular, Bold and Semi-Bold.
2. Some corners radius. There two dimensions that most UI elements uses - "small" and "big" corners radius.
3. Some colors. Gradient start-end colors, brand colors. Both light and dark themes could be provided.

So how to change this properites? You need to create resources in your application xml files named like in table below:

| Parameter | Properties name                            | Default value                                       |
|-----------|--------------------------------------------|-----------------------------------------------------|
| Color     | shoppablevideo_brand_primary_fill_alpha15  | #260091FF - light and night                         |
| Color     | shoppablevideo_brand_primary               | #0091FF - light and night                           |
| Color     | shoppablevideo_brand_secondary             | #EF5DA8 - light and night                           |
| Color     | shoppablevideo_gradient_start_color        | #FF0091FF - light and night                         |
| Color     | shoppablevideo_gradient_end_color          | #FF00D1FF - light and night                         |
| Dimension | shoppablevideo_buttons_small_corner_radius | 10dp                                                |
| Dimension | shoppablevideo_buttons_big_corner_radius   | 15dp                                                |
| Font      | shoppablevideo_bold_font.ttf               | https://fonts.google.com/specimen/Inter?query=inter |
| Font      | shoppablevideo_regular_font.ttf            | https://fonts.google.com/specimen/Inter?query=inter |
| Font      | shoppablevideo_semi_bold_font.ttf          | https://fonts.google.com/specimen/Inter?query=inter |

For example to override color `shoppablevideo_brand_primary` you can add resource to your xml file where you colors:
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
   <color name="shoppablevideo_brand_primary">#0091FF</color>
</resources>
```

If you want to override fonts - create font resource folder and put your ttf file there with appropriate name (look at the table above):
