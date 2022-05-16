### Warning

All code samples in this document presented using kotlin language (kotlin DSL for gradle scripts).

1. [How to install](#how-to-install)
2. [LiveCom methods and parameters](#livecom-methods-and-parameters)
3. [SDK exit points](#sdk-exit-points)
4. [Caveats](#caveats)

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
4. To open screen with video list, call:

   ```kotlin
   LiveCom.openVideoListScreen(activity: Activity)
   ```

   To open exact video immediate, call:

   ```kotlin
   LiveCom.openVideoById(activity: Activity, streamId: String)
   ```

# LiveCom methods and parameters

### `LiveCom.configure()`

This method remembers your sdk token and domain to generate share link. Also it downloads localization, images and some other configuration from sdk api in order to work correctly.

Parameters:

* `applicationContext: Context` - Android application context;
* `sdkToken: String` - your sdk token, received from company manager;
* `shareDomain: String` - domain that will be used to generate links like: `https://$shareDomain/s/$videoId`. Share feature available on player screen and product screen;

### `LiveCom.openVideoListScreen()`

Opens screen with all your available videos. Technically it starts new activity, that will manage internal sdk fragments navigation. Video list fragment is one of them. Note that this method may suspend thread if sdk configuration loading is not completed yet.

Parameters:

* `activity: Activity` - your current activity where you call this method.

### `LiveCom.openVideoById()`

Opens player with passed streamId. Video list screen will be below player in navigation stack.  Note that this method may suspend thread if sdk configuration loading is not completed yet.

Parameters:

* `activity: Activity` - your current activity where you call this method.
* `streamId: String` - video id, that you want to open

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
