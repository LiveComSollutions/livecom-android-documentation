## Errors solutions when integrating sdk

##### 1\. Could not found com.mux.stats.sdk.muxstats

Reason: You did not set maven repository for this dependency. We use [this sdk](https://github.com/muxinc/mux-stats-sdk-exoplayer) to collect ExoPlayer perfomance and statistic.

Solution: Add all needed maven repos as written in [“How to Install”](https://dividio.atlassian.net/wiki/spaces/DED/pages/86867988/Android+SDK#How-to-install) section

##### 2\. The minCompileSdk (33) specified in a dependency's AAR metadata is greater then this module's compileSdkVersion

Reason: Our sdk is built with android `minCompileSdk` parameter equal to 33. This is the latest version at the moment we write this documentation. We need to be up to date with the latest Android features and bug fixes.

Solution: If you have a lower version, please consider increasing it up to 33. It is highly recommended to keep this number up to date. If you have a higher version, then please contact us and we will update our sdk for you.

##### 3\. Module was compiled with an incompatible version of Kotlin. The binary version of its metadata is 1.7.1, expected version is 1.5.1

Reason: This error occurs if you have restricted Kotlin language version to some lower than 1.7.1. In SDK we use Kotlin version 1.7.10, but we set [apiVersion and languageVersion](https://kotlinlang.org/docs/gradle-compiler-options.html#attributes-common-to-jvm-and-js) to 1.5, so our code is fully compatible with Kotlin 1.5. But if you restrict Kotlin version (for example using [resolution strategy](https://developer.android.com/studio/build/dependencies#custom_dep_resolutions)), gradle can build project with two different Kotlin versions.

Solution: Please consider removing version restriction in your project. This is not a good practice to use older versions.

##### 4\. N files found with path 'META-INF/LICENSE.md' from inputs: ...

Reason: Several libraries package their own license file into their aar. So gradle does not know how to solve this conflict of the same file names

Solution: Please add the code below to your application module `build.gradle` file:

```plaintext
packagingOptions {
    resources.excludes.add("META-INF/*")
}
```
