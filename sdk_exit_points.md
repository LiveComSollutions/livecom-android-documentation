> SDK starts its own activity when you call `LiveCom.openSdkScreen`. So, if you want to show some UI on top of SDK screens, you have two options:
> 
> 1.  Open new activity and handle your UI/navigation there
> 2.  Reorder some of your existing activities to front. But be sure to preserve navigation patterns here, so when user navigates back he could see correct screens sequence.

> According to [Android Application lifecycle](https://developer.android.com/guide/components/activities/process-lifecycle) any process can be killed by the system. All static variables and other state will be lost when this happens. Developer can save some primitive, Serializable and Parcelable objects in Bundle. But `LiveCom.callback` (read below) is not one of them. That is why it can not be serialized. And that is why this variable is separated from `LiveCom.configure()` method. It's the sdk user (you - whoever is reading this now) responsibility to set this callback again after the application is recreated by the system. Otherwise you will lose control over SDK navigation flow.

The SDK contains several screens for users. Videos list, Player, Product screen, Checkout and ThankYou page. They can all be reached by user by default. But if you want to show your own Product and/or Checkout screens, you have such a possibility. `LiveCom` singleton has public `var` field `callback`. You can implement `LiveCom.Callback` interface and set this implementation to `LiveCom.callback` variable.

This is how the Callback interface is declared in the SDK:

```kotlin
object LiveCom {
   
   var callback: Callback = object : Callback {
        override fun openProductCardInsideSdk(productId: String): Boolean = true

        override fun openCheckoutInsideSdk(productsInCart: List<LiveComProductInCart>): Boolean =
            true
        
        fun productsInCartChanged(productsInCart: List<LiveComProductInCart>) = Unit
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
        
        /**
         * @param productsInCart product ids, selected parameters and quantity that user added to cart
         */
        fun productsInCartChanged(productsInCart: List<LiveComProductInCart>)
    }
}
```

Default `callback` implementation always returns `true` from both methods. This means that internal SDK Product and Checkout screens will be opened when needed. If your implementation of these methods returns `false` (from one or both methods), then the corresponding screen will not be shown. And it's your responsibility to show some screen.

Both methods have arguments.

1.  `openProductCardInsideSdk` has `productId`. This is SKU of product that we want to open on Product screen.
2.  `openCheckoutInsideSdk` has `productsInCart` list of `LiveComProductInCart` data objects.
3.  `productsInCartChanged` has `productsInCart` list of `LiveComProductInCart` data objects. This method is invoked each time when something happens with products in the cart (add, remove, change count etc.).
