# Firefox Reality

The goal of the Firefox Reality project is to create a full-featured browser exclusively for *standalone* AR and VR headsets.

You can find us on Twitter [@MozillaReality](https://twitter.com/mozillareality) and at [mixedreality@mozilla.com](mailto:mixedreality@mozilla.com).

[![Task Status](https://github.taskcluster.net/v1/repository/MozillaReality/FirefoxReality/master/badge.svg)](https://github.taskcluster.net/v1/repository/MozillaReality/FirefoxReality/master/latest) [Build results](https://github.taskcluster.net/v1/repository/MozillaReality/FirefoxReality/master/latest)

## Download developer APKs

**[Download](https://github.taskcluster.net/v1/repository/MozillaReality/FirefoxReality/master/latest)** [bleeding-edge](https://github.com/MozillaReality/FirefoxReality/commits/master) developer APKs for Firefox Reality.

### Advanced

1. Load [this TaskCluster URL](https://github.taskcluster.net/v1/repository/MozillaReality/FirefoxReality/master/latest).
2. Click the `Firefox Reality for Android - Build - Master update →` link.
3. Click the `Run Artifacts` tab, and click to download the APK for your platform of choice.

## Locale support

Firefox Reality supports Chinese (China), Chinese (Taiwan), English, French, German, Italian, Japanese, Russian, and Spanish for user interface, voice search, and text entry. Localization is provided by Mozilla's amazing community of volunteer localizers.

For more info on localization, how it works in the Firefox Reality project, and how to correctly edit localizable text in the application, please see the [localization wiki page](https://github.com/MozillaReality/FirefoxReality/wiki/Localization).

## Setup instructions

*Clone FirefoxReality.*

```bash
git clone git@github.com:MozillaReality/FirefoxReality.git
cd FirefoxReality
```

*Clone the third-party repo.*

If you're developing for the Oculus, Snapdragon VR, or VIVE, you need to clone the repo with third-party SDK files.

```bash
git clone git@github.com:MozillaReality/FirefoxReality-android-third-party.git third_party
```

This repo is only available to Mozilla employees. If you have access to the relevant SDK but not this repo, you can manually place them here:

 - `third_party/ovr_mobile/` for Oculus (should contain a `VrApi` folder)
 - `third_party/svr/` for Snapdragon (should contain a `libs` folder, among other things)
 - `third_party/wavesdk/` for Vive (should contain a `build` folder, among other things)

The [repo in `third_party`](https://github.com/MozillaReality/FirefoxReality-android-third-party) can be updated like so:

```bash
pushd third_party && git fetch && git checkout master && git rebase origin/master && popd
```

*Fetch Git submodules.*

You may need to set up [two-factor authentication](https://blog.github.com/2013-09-03-two-factor-authentication/#how-does-it-work-for-command-line-git) for the command line.

```bash
git submodule update --init --recursive
```

You can build for different devices:

- **`oculusvr`**: Samsung Gear VR & Oculus Go
- **`svr`**: Qualcomm & ODG glasses
- **`wavevr`**: VIVE Focus
- **`googlevr`**: Google Daydream

For testing on a non-VR device:

- **`noapi`**: Runs on standard Android phones without a headset

Building for Oculus Mobile, SVR, and WaveVR requires access to their respective SDKs which are not included in this repo.

*If you want to build FirefoxReality for Google Daydream (`googlevr`), you need to run:*

```bash
cd gvr-android-sdk && ./gradlew :extractNdk
```

The command line version of `gradlew` requires [JDK 8 from Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). If you see an error that Gradle doesn't understand your Java version, check which version of you're using by running `java -showversion` or `java -version`. You're probably using JDK 9 or 10, which won't work.

If you get an error extracting the NDK, you might need to copy the `local.properties file` from the top-level project directory into the `gvr-android-sdk` directory. If this file doesn't exist at the top-level directory either, open the top-level directory in Android Studio, and it should be created.

*Open the project with [Android Studio](https://developer.android.com/studio/index.html)* then build and run it. Depending on what you already have installed in Android Studio, the build may fail and then may prompt you to install dependencies. Just keep doing as it suggests. To select the device to build for, go to `Tool Windows > Build Variants` and select a build variant corresponding to your device.

If you run the APK on an Android device outside of Daydream or Gear VR, it will run in flat mode. To run in VR, put the device into a headset, and run the app from the VR launcher.

Unfortunately, for mobile-clip-in VR viewers, the APK isn't yet published in the Google Play Store because of [known performance issues and bugs](https://github.com/MozillaReality/FirefoxReality/issues/598). The APK currently available is for only standalone Daydream headsets.

*If you want to build FirefoxReality for WaveVR SDK:*

Download the [VIVE Wave SDK](https://developer.vive.com/resources/knowledgebase/wave-sdk/) from the [VIVE Developer Resources](https://vivedeveloper.com/), and unzip it. Then, from the top-level project directory, run:

```bash
mkdir -p third_party/wavesdk
cp /path/to/the/sdk/2.0.32/SDK/libs/wvr_client.aar third_party/wavesdk
cp ./extra/wavesdk/build.gradle ./third_party/wavesdk
```

Make certain to set the build flavor to `wavevrDebug` in Android Studio before building the project.

## Using a custom GeckoView

Create a file called `user.properties` in the top-level project directory. Add a variable called `geckoViewLocalArm` and `geckoViewLocalX86` and set it to the location of your locally built AAR:

```ini
geckoViewLocalArm=/path/to/your/build/geckoview-nightly-armeabi-v7a-64.0.20180924100359.aar
geckoViewLocalX86=/path/to/your/build/geckoview-nightly-x86-64.0.20180924100359.aar
```

## Install dev and production builds on device simultaneously

You can enable a dev applicationID sufix to install both dev and production builds simultaneously. You just need to add this property to your `user.properties` file:

```ini
simultaneousDevProduction=true
```
## Locally generate Android release builds

Local release builds can be useful to measure performance or debug issues only happening in release builds. Insead of dealing with release keys you can make the testing easier just adding this property to your `user.properties` file:

```ini
useDebugSigningOnRelease=true
```

Note: the release APKs generated with a debug keystore can't be used for production.

## Compress assets

ETC2 compression is used to improve performance and memory usage. Raw assets are placed in the `uncompressed_assets` folder. You can generate the compressed textures using the compressor utility in `tools/compressor`. You need to set up [etc2comp](https://github.com/google/etc2comp) and make it available on your PATH before running the script. Run this command to generate the compressed assets:

```bash
cd tools/compressor
npm install
npm run compress
```

## Development troubleshooting

### `Device supports , but APK only supports armeabi-v7a[...]`

Enable [USB Remote Debugging](https://github.com/MozillaReality/FirefoxReality/wiki/Developer-Info#remote-debugging) on the device.

### `Could not get unknown property 'geckoViewLocal' for build 'FirefoxReality'[...]`

```bash
./mach build
./mach package
./mach android archive-geckoview
find $objdir -name *.aar
echo "geckoViewLocalArm=$objdir/gradle/build/mobile/android/geckoview/outputs/aar/geckoview-official-withGeckoBinaries-noMinApi-release.aar" > $FirefoxReality/user.properties
```

### **`Firefox > Web Developer > WebIDE > Performance`** gets stuck with greyed out "stop and show profile"

Restart FxR and close and re-open the WebIDE page.

### **`Tool Windows > Build Variants`** list is empty

1. If you're not on the latest version, update Android Studio from **`Android Studio > Check for Updates…`**.
2. Run **`File > Sync Project with Gradle Files`**.

## Debugging tips

- When using the native debugger you can ignore the first SIGSEGV: address access protected stop in GV thread. It's not a crash; you can click *Resume* to continue debugging.
- On some platforms such as Oculus Go the native debugger stops on each input event. You can set this LLDB post-attach command in Android Studio to fix the problem: `pro hand -p true -s false SIGILL`
- You can use `adb shell am start -a android.intent.action.VIEW -d "https://aframe.io" org.mozilla.vrbrowser/org.mozilla.vrbrowser.VRBrowserActivity` to load a URL from the command line
- You can use `adb shell am start -a android.intent.action.VIEW  -n org.mozilla.vrbrowser/org.mozilla.vrbrowser.VRBrowserActivity -e homepage "https://example.com"` to override the homepage
- You can use `adb shell setprop debug.oculus.enableVideoCapture 1` to record a video on the Oculus Go. Remember to run `adb shell setprop debug.oculus.enableVideoCapture 0` to stop recording the video.
    - You can also record videos on the Oculus Go by exiting to the system library, and from the Oculus tray menu (toggle with the Oculus button on the controller): **`Sharing > Record Video`**
- You can set `disableCrashRestart=true` in the gradle `user.properties` to disable app relaunch on crash.

## Experimental Servo support

To compile with Servo support, create a file called `user.properties` in the top-level project directory and add `enableServo=1`. Then to enable Servo in Firefox Reality, go the Developer Options panel in the Settings, and toggle the Servo option. Then a new button will be added to the navigation bar. Clicking that button will reload the current page with Servo.
