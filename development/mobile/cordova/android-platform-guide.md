### Cordova - Android Platform Guide

A brief overview of install and setup for Android with Cordova.

#### Contents
* Intro
* Check Cordova requirements
* Android requirements and environment setup
* Create basic app
* Add Android platform
* Build basic app
* Emulate app

#### Intro
The following brief guide assumes you have `Node.js`, `NPM`, and `Git` installed and working correctly on your local system. Then, you'll need to ensure you have the latest version of Cordova installed and working correctly.

#### Check Cordova requirements
These checks and requirements are per platform, for example for development with Android.

Initially, we can check required and installed dependencies for Cordova with the following command,

```bash
cordova requirements
```

This command will check build requirements for each install platform, e.g. Android.

#### Android requirements and environment setup
Before installing and configuring Android, we need to ensure that the latest version of the Java SDK is available,

* update java to latest se version, e.g. 1.8.0_102
* update `~/.bash_profile`

```
# Create a JAVA_HOME variable, location determined dynamically as part of install
export JAVA_HOME=$(/usr/libexec/java_home)
# Add that to the global PATH variable
export PATH=${JAVA_HOME}/bin:$PATH
```

**n.b.** this will add Java path &c. for latest Java version...

Then, we can start to setup our Android environment,

* download Android Studio - [https://developer.android.com/studio/index.html](https://developer.android.com/studio/index.html)
  * install and follow prompts for default install and setup
* if installing just the Android SDK, add the directory location of the SDK's `tools` and `platform-tools` to `PATH`

e.g.

  * add ANDROID_HOME to path in `~/.bash_profile`

```
export ANDROID_HOME=~/Development/android-sdk/
export PATH=$PATH:~/Development/android-sdk/platform-tools/
export PATH=$PATH:~/Development/android-sdk/tools/
export PATH=$PATH:~/Development/android-sdk/build-tools/
```

* then setup an Android Virtual Device (**AVD**) for use as an emulator
  * follow guide at https://developer.android.com/studio/run/managing-avds.html
* open Android Studio
  * select option for AVD manager from toolbar for open project (normally 4th from the right)
  * follow prompts and choose preferred specific device or generic device type
  * choose required Android platform, e.g. Android 6.0, SDK 23
* Or, we can issue the following command to use the AVD manager directly

```bash
android avd
```

However, latest AVD definitions (incl. Nexus 5x, 6P...) are available using AVD manager via Android Studio.

#### Create basic app
To test the new Cordova install, we can now create a simple initial app

```bash
cordova create test com.example.test TestApp
```

This command will create a new app with its own basic, shell template for the app's underlying structure. The home page for this app will be stored in `www/index.html`.

#### Add Android platform
For a chosen project, we can then add required platforms for our application. These are the mobile OSs the app needs to run on, and Cordova will support. We can add the Android platform with the following command from within the root directory of our Cordova app,

```bash
cordova platform add android --save
```

We can then check all current install platforms with the following command,

```bash
cordova platform ls
```

Each platform added to a project is stored in the app's `/platforms/` directory. These files are maintained and updated as part of the build and update process run by Cordova for the current app.

#### Build basic app
After creating our basic test app, we can now build it from the directory created for the app.

```bash
cordova build
```

This command will build the current app for the installed platforms. However, we may also wish to limit the scope of a given build to a specific platform, e.g.

```bash
cordova build android
```

<div style="page-break-after: always;"></div>

#### Emulate app
We can also build our app, and then open it for testing with an installed emulator

```bash
cordova emulate android
```

This will choose the default installed emulator for the chosen platform. However, we may also wish to add a flag for a specific emulated device,

```bash
cordova emulate android --target=Nexus_6P_API_23
```

This will load the app in the specific emulator for the Nexus 6P. Example app loaded in emulator,

![Cordova - Android emulator](../media/images/cordova-android-emulate.png)
