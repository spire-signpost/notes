### Cordova - CLI - Useful Commands

A list of useful commands for working with Cordova's CLI tool.

#### Content
* Basic
* Install and setup
* Building the app
* Test the app

#### Basic
* check current installed Cordova version

```bash
cordova -v
```

#### Install and setup
* create an app with a basic structure,

```bash
cordova create hello com.example.hello HelloWorld
```

* add platforms to your project

```bash
cordova platform add android --save
cordova platform add ios --save
```

* check current platforms - lists currently installed and available

```bash
cordova platform ls
```

* check installed requirements - platform dependent
  * requirements will checked relative to installed platforms for current project development
  * outputs whether JDK, Android SDK, Gradle &c. are installed

```bash
cordova requirements
```

#### Building the app
* build a Cordova app for all included platforms

```bash
cordova build
```

* optionally limit the scope of a build to a specific platform, e.g. Android

```bash
cordova build android
```

#### Test the app
* check available emulators, e.g. Android AVDs

```bash
cordova run --list
```

* rebuild the current app and view using the specified platform's emulator

```bash
cordova emulate android
```

& then update the current emulated image with the following command,

```bash
cordova emulate
```

* rebuild the current app and launch on a connected device (phone, tablet &c.)

```bash
cordova run android
```

##### Useful CLI commands

| command | example | description |
|---------|---------|-------------|
| cordova | cordova | general command - outputs overview with 5 categories of information and help |
| -v | cordova -v | check current installed version of cordova |
| requirements | cordova requirements | check requirements for each installed platform |
| create | cordova create basic com.example.basic 422Basic | creates new project with additional arguments for directory name, domain-style identifier, and the app's display title |
| platform add | cordova platform add android --save | specify target platforms, eg: Android, iOS... (NB: SDK support required on local machine)|
| platform ls | cordova platform ls | checks current platforms for cordova development on local machine and lists those available|
| platform remove (platform rm) | cordova platform rm android | remove an existing platform |
| build | cordova build | iteratively builds the project for the available platforms |
| build platform | cordova build ios | limit scope of build to a specific platform (useful for testing a single platform...)
| prepare | cordova prepare ios | prepare a project, and then open and build etc with native IDE (eg: XCode, Android Studio...)
| compile | cordova compile ios | compile ios specific version of app |
| emulate | cordova emulate android | rebuilds an app and then launches it in a specific platform's emulator |
| run | cordova run android | run an app on a native device connected to the local machine |
| run --list | cordova run --list | check available emulators, e.g. Android AVDs |

**n.b.** choice of platform interchangeable in above commands, e.g.

```bash
cordova build android
cordova build ios
```
