# Plaid Link for Flutter

[![pub](https://img.shields.io/pub/v/plaid_flutter.svg)](https://pub.dev/packages/plaid_flutter)
[![donate](https://img.shields.io/badge/Buy%20me%20a%20beer-orange.svg)](https://www.buymeacoffee.com/jpereira)

A Flutter plugin for [Plaid Link](https://github.com/plaid/link).

This plugin integrates the native SDKs:

- [Plaid Link iOS SDK](https://github.com/plaid/plaid-link-ios)
- [Plaid Link Android SDK](https://github.com/plaid/plaid-link-android)

Feel free to leave any feedback [here](https://github.com/jorgefspereira/plaid_flutter/issues).

## Requirements

In order to initialize Plaid Link, you will need to create a link_token at [/link/token/create](https://plaid.com/docs/#create-link-token). After generating a link_token, you'll need to pass it into your app and use it to launch Link:

``` dart
...

LinkConfiguration configuration = LinkConfiguration(
    linkToken: "<GENERATED_LINK_TOKEN>",
);

_plaidLink = PlaidLink(
    configuration: configuration,
);

_plaidLink.open();

...

```

A link_token can be configured for different Link flows depending on the fields provided during token creation. It is the preferred way of initializing Link going forward. You will need to pass in most of your Link configurations server-side in the [/link/token/create](https://plaid.com/docs/#create-link-token) endpoint rather than client-side where they previously existed.

If your integration is still using a public_key to initialize Plaid Link, the LinkConfiguration class has support for it. Check the [migration guide](https://plaid.com/docs/upgrade-to-link-tokens/) to upgrade your app to the link_token flow.

## Installation

Add `plaid_flutter` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).

## iOS

### 1. Project configuration

Go to your xcode project and make sure you are targeting iOS version >= 9.0

### 2. Configure to Release

Add a Run Script build phase *(name it Prepare for Distribution for example)* with the script below. Be sure to run this build phase after the Embed Frameworks build phase (or [CP] Embed Pods Frameworks build phase when integrating using CocoaPods)

``` sh
LINK_ROOT=${PODS_ROOT:+$PODS_ROOT/Plaid}
cp "${LINK_ROOT:-$PROJECT_DIR}"/LinkKit.framework/prepare_for_distribution.sh "${CODESIGNING_FOLDER_PATH}"/Frameworks/LinkKit.framework/prepare_for_distribution.sh
"${CODESIGNING_FOLDER_PATH}"/Frameworks/LinkKit.framework/prepare_for_distribution.sh
```

![](https://raw.githubusercontent.com/jorgefspereira/plaid_flutter/master/doc/images/edit_run_script_build_phase.jpg)

The script below removes any non-iOS device code from the framework which is included to support running LinkKit in the Simulator but may not be distributed via the App Store.

*NOTE: More info at [https://plaid.com/docs/link/ios](https://plaid.com/docs/link/ios).*

## Android

### 1. Register Package Name

Log into your Plaid Dashboard at the API page and add a new Allowed Android package name *(for example com.plaid.example)*

![](https://raw.githubusercontent.com/jorgefspereira/plaid_flutter/master/doc/images/register-app-id.png)

### 2. Gradle Configuration

Go to the project level `android/app/build.gradle` and make sure you are using a minSdk >= 21

*NOTE: More info at [https://plaid.com/docs/link/android](https://plaid.com/docs/link/android).*

### 3. Configure to Release

Before creating the release build of your app (which is the default setting when building an APK or App Bundle) you will likely need to add the following line to your ProGuard configuration file as per this [link](https://developer.android.com/studio/build/shrink-code#keep-code) to avoid runtime crashes when the app is built with shrink enabled:

```
-keep class com.google.crypto.tink.proto.** { *; }
```

*NOTE: The example app is updated with a Proguard rules file(`proguard-rules.pro`)[here](https://github.com/jorgefspereira/plaid_flutter/tree/master/example/android/app/proguard-rules.pro).*

## TODOs
- [ ] Web support 
- [ ] [Avoid iOS Prepare for distribution configuration](https://plaid.com/docs/link/ios/#prepare-distribution-script)
- [ ] Implement tests