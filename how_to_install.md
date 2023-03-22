In your android project `settings.gradle` file add a repository (to browse repo click here):

```plaintext
dependencyResolutionManagement {
	repositories {
		...
		// sdk located here
		maven("https://nexus.livecom.tech/repository/maven-android/")
		// sdk uses https://github.com/muxinc/mux-stats-sdk-exoplayer for statistics
		maven("https://muxinc.jfrog.io/artifactory/default-maven-release-local")
	}
}
```

Inside your module (where you want to add sdk dependency) `build.gradle` file add:

```plaintext
dependencies {
	....
	implementation("com.livecommerceservice:sdk:{latest_version}")
}
```

In your project, make a call:

```kotlin
import com.livecommerceservice.sdk.domain.api.LiveCom

LiveCom.configure(
	appContext: Context,
	sdkToken: String,
	shareDomain: String
)
```

Call this method as soon as possible. Because it needs time to load some sdk configuration from network. Methods and parameters described [further](test).

To open sdk start screen call:

`LiveCom.openSdkScreen(entranceCommand: SdkEntrance, context: Context)`

More about this method read [here](test).

### Enable Java 8+ API desugaring

The SDK internally uses a number of Java 8 language APIs through desugaring. Make sure your project either enables desugaring, or requires a minimum API level of 26. Please refer to [this](https://developer.android.com/studio/write/java8-support#library-desugaring) document to find out how to do it.
