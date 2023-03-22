## Google Maps integration

LiveCommerce SDK has Google Maps dependency. In order to open maps you need to select “Pickup” delivery type on the checkout screen. Pickup bottom sheet appears and contains “Map” button. But if you want this button to be visible, you should include such block in `AndroidManifest.xml`:

```plaintext
<meta-data
     android:name="com.google.android.geo.API_KEY"
     android:value="${MAPS_API_KEY}" />
```

Replace MAPS\_API\_KEY with a key you received in Google Cloud Console (GCC) for Google Maps. How to get this key is written [here](https://developers.google.com/maps/documentation/android-sdk/get-api-key).

Api key could leek into Git if you insert it directly into AndroidManifest.xml. This is not the best case. Integration apps can use GitHub - [google/secrets-gradle-plugin: A Gradle plugin for providing your secrets to your Android project](https://github.com/google/secrets-gradle-plugin) to avoid this. We use this in our demo application (Beauty Shopping Faster).  
Inside the pickup bottom sheet we check if the manifest contains correct block with meta-data. The map button becomes visible if correct meta-data exists. The button is hidden if correct meta-data is not found.  
Developer who integrates LiveCom sdk is responsible for this meta-data block in his application. If meta-data block is not added, then everything will work without Google Maps.
