# THEOplayerSDK

THEOplayer is the universal video player solution created by [THEO Technologies](https://www.theoplayer.com/).
The THEOplayer Android SDK enables you to quickly deliver content playback on Android, Android TV and Fire TV.

## Prerequisites

### THEOplayer *Android* vs. *Unified Android* SDK

The THEOplayer SDK for Android comes in different flavours. One group consists of the separate SDKs for Android (mobile) 
minApi16 and minApi21, Android TV and Fire TV, which are all variants that differ from one another by how 
per-platform specifics are handled.

More recently, a new SDK was released that covers all platforms with a single library: the *Unified Android* SDK.
It supports clear and encrypted playback through (LL-)DASH, (LL-)HLS and HESP on Android, Android TV and FireTV.
From API perspective, while it does not yet contain all features in comparison to the other SDKs, 
it aims to be interchangeable.

### THEOplayer license

A valid [THEOplayer license](https://docs.theoplayer.com/how-to-guides/12-license/00-introduction.md)
is required to use a THEOplayer SDK. 
It can be requested via [THEOportal](https://portal.theoplayer.com).

With the exclusion of the Unified Android SDK, you can also create a custom THEOplayer Android SDK,
Android TV SDK or Fire TV SDK in THEOportal.
After creating the custom SDK, copy its license string to your clipboard as 
demonstrated in the screenshot below. Use this license string when setting up THEOplayer.

![](https://cdn.theoplayer.com/images/git/theoplayer-android-sdk-license-string.png)

## Included features

#### Default packages

The THEOplayer SDK consists of [modular features](https://docs.theoplayer.com/faq/53-theoplayer-features-modules.md).
All packages include only the basic features. 
The `unified` package is built with features ```DASH, HLS, LL-HLS```, 
while ```DASH, HLS, LL-HLS, UI, Cache, ExoPlayer``` applies for all other packages.
Additional feature sets will be provided in the future.

Alternatively, except for `unified`, you can make your own custom build via our [THEOportal](https://portal.theoplayer.com),
and manually include the THEOplayer SDK.

#### Customize unified package

To customize your build of the `unified` package, you can add additional dependencies. For example, if you would like to add Google IMA advertisements to your player you can add the `unified-ads-ima:+` dependency. Additional feature dependencies will be added in the future and listed here.

## Installation

In your **project** level `build.gradle` file add the Jitpack repository:

```
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```

In your **module** level `build.gradle` file add one or more THEOplayer dependencies:

```
implementation 'com.theoplayer.theoplayer-sdk-android:basic-minapi16:+'
implementation 'com.theoplayer.theoplayer-sdk-android:basic-minapi21:+'
implementation 'com.theoplayer.theoplayer-sdk-android:basic-androidTV:+'
implementation 'com.theoplayer.theoplayer-sdk-android:basic-fireTV:+'
implementation 'com.theoplayer.theoplayer-sdk-android:unified:+'
```

If you're targeting Android 5.0 and above, then you should only add the `minapi21` dependency.
If you're also targeting Android 4.1 and above, you should also add the `minapi16` dependency, and you'll find the article at [https://docs.theoplayer.com/getting-started/01-sdks/02-android/02-android-sdk-product-flavors.md](https://docs.theoplayer.com/getting-started/01-sdks/02-android/02-android-sdk-product-flavors.md) to be interesting.

If you're targeting Android TV, then you should only add the `androidTV` dependency.

If you're targeting Fire TV, then you should only add the `fireTV` dependency.

The `unified` dependency targets all Android, AndroidTV and Fire TV platforms from Android 4.1 upwards.

Notes:

* The `+` will fetch the latest released version of THEOplayer SDK.
* Android Studio will recommend replacing the `+` with the exact version of THEOplayer.
* Versions earlier than 2.83.0 (release 2021.1.3) are not available on the public Jitpack registry.
Earlier versions are available through our [THEOportal](https://portal.theoplayer.com).

## Usage

### 1. Instantiation

The UI element of THEOplayer is called `THEOplayerView` and it can be instantiated in 2 different ways: through A) XML or through B) the THEOplayer constructor API.

Note the `"your_license_here"` placeholder string in these two examples.
You want to replace this placeholder string with the license string mentioned in the "Prerequisites".

#### A) Using the layout.xml

You can instantiate a THEOplayer instance through XML, as demonstrated by the snippet below.

```xml
<com.theoplayer.android.api.THEOplayerView
    android:id="@+id/theoPlayerView"
    android:layout_width="match_parent"
    android:layout_height="400dp" />
```
And then, in the Activity/Fragment, you can reference to it, as demonstrated by the snippet below.

```java
THEOplayerView theoPlayerView = findViewById(R.id.theoPlayerView);
```

Furthermore, you need to configure the license string mentioned in "Prerequisites" in your `AndroidManifest.xml`.
This file should resemble the snippet below.

```xml
<application>
    <meta-data
        android:name="THEOPLAYER_LICENSE"
        android:value="your_license_here" />
</application>
```

#### B) Using the constructor API ####

In the Activity/Fragment, you can use the [constructor API](https://docs.theoplayer.com/api-reference/android/index.html?com/theoplayer/android/api/THEOplayerView.html) to create a THEOplayer instance
by passing along a context and a [`THEOplayerConfig`](https://docs.theoplayer.com/api-reference/android/index.html?com/theoplayer/android/api/THEOplayerConfig.html).

In this `THEOplayerConfig`, you must configure your license string, as demonstrated in the snippet below.

```java
THEOplayerConfig playerConfig = new THEOplayerConfig.Builder()
    .chromeless(false)
    .license("your_license_here")
    .build();
THEOplayerView theoPlayerView = new THEOplayerView(this, playerConfig);
```

### 2. Calling lifecycle events

```java
@Override
protected void onPause() {
    super.onPause();
    theoPlayerView.onPause();
}

@Override
protected void onResume() {
    super.onResume();
    theoPlayerView.onResume();
}

@Override
protected void onDestroy() {
    super.onDestroy();
    theoPlayerView.onDestroy();
}
```

### 3. Using the player

```java
Player theoPlayer = theoPlayerView.getPlayer();
```

#### Creating and setting a source

```java
TypedSource typedSource = new TypedSource.Builder()
    .src(getString(R.string.defaultSourceUrl))
    .build();

SourceDescription sourceDescription = new SourceDescription.Builder()
    .sources(typedSource)
    .poster(getString(R.string.defaultPosterUrl))
    .build();

theoPlayer.setSource(sourceDescription.build());
```

### 4. Adding listeners to THEOplayer basic playback events.

```java
theoPlayer.addEventListener(PlayerEventTypes.PLAY, event -> Log.i(TAG, "Event: PLAY"));
theoPlayer.addEventListener(PlayerEventTypes.PLAYING, event -> Log.i(TAG, "Event: PLAYING"));
theoPlayer.addEventListener(PlayerEventTypes.PAUSE, event -> Log.i(TAG, "Event: PAUSE"));
theoPlayer.addEventListener(PlayerEventTypes.ENDED, event -> Log.i(TAG, "Event: ENDED"));
theoPlayer.addEventListener(PlayerEventTypes.ERROR, event -> Log.i(TAG, "Event: ERROR, error=" + event.getErrorObject().getMessage()));
```

That's it! You should now have a working player in your application.

## Documentation

The documentation for THEOplayer is located on our [documentation website](https://docs.theoplayer.com).
For an example on how to setup THEOplayer, take a look at our [Getting started guide](https://docs.theoplayer.com/getting-started/01-sdks/02-android/00-getting-started.md).

## Support

If you are having issues installing or using the package, first look for existing answers on our [documentation website](https://docs.theoplayer.com/),
and in particular our [FAQ](https://docs.theoplayer.com/faq/00-introduction.md).

You can also contact our technical support team by following the instructions on our [support page](https://docs.theoplayer.com/faq/00-introduction.md).
Note that your level of support depends on your selected [support plan](https://www.theoplayer.com/supportplans).

## License

The contents of this package are subject to the [THEOplayer license](https://www.theoplayer.com/terms).
