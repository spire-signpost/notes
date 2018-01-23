### Cordova - Guide - iOS Platform Guide

A brief overview of install and setup for iOS with Cordova, primarily targeting cross-platform iOS development.

#### Contents
* Intro
* Install and setup
* Create basic app
* Build and emulate

#### Intro
The following brief guide assumes you have `Node.js`, `NPM`, and `Git` installed and working correctly on your local system. Then, you'll need to ensure you have the latest version of Cordova installed and working correctly.

**n.b.** the following is sufficient to emulate and run a cross-platform Cordova app on iOS.

To create an iOS-only specific Cordova-based app, you'll also need to satisfy standard iOS development requirements for working with an iOS app in Xcode. This includes the required iOS SDK support, Apple Developer account, and so on. After building the Cordova app for iOS, you can then open the app in Xcode and modify the iOS specific code.

#### Install and setup
* install latest version of Xcode - available from Mac App Store
  * follow install prompts
  * install additional components - check command line tools are installed

```
xcode-select --install
```

* update `ios-deploy` if needed for the local system

```bash
npm install -g ios-deploy
```

* update `ios-sim` if needed for the local system

```bash
npm install -g ios-sim
```

**n.b.** if you receive any build errors for iOS `emulate` or `run` commands, e.g.

```bash
Error: Cannot read property 'replace' of undefined
```

you'll need to `cd` to your Cordova app directory, and then install `ios-sim` as follows,

```bash
cd platforms/ios/cordova && npm install ios-sim
```

This means we can now uninstall the global install for `ios-sim` if necessary.

<div style="page-break-after: always;"></div>

#### Create basic app

* create a standard Cordova project, e.g.

```bash
cordova create test com.example.test TestApp
```

* then add iOS platform to that project - `cd` to the directory for that project

```bash
cd test
cordova platform add ios --save
```

#### Build and emulate
* check Cordova requirements for install platforms

```bash
cordova requirements
```

e.g. you may need to install CocoaPods. Simply follow the instructions at the following URL,

  * https://cocoapods.org/

```bash
sudo gem install cocoapods
```

Then, you'll need to sync the *CocoaPods* repo. This might take a while (many minutes for some systems), depending on the speed of your current internet connection and your system's performance.

* if Cordova requirements have been met
  * build app for iOS platform

```bash
cordova build iOS
```

* then load app in iOS emulator

```bash
cordova emulate ios
```

**n.b.** if no specific target device, app will open in default emulator, e.g. iPhone-6s-Plus, 9.3 simulator

<div style="page-break-after: always;"></div>

Example app loaded in emulator,

![Cordova - iOS emulator](../media/images/cordova-ios-emulate.png)
