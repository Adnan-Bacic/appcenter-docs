---
title: HockeySDK for macOS Migration
description: Migrate from the HockeySDK to App Center SDK for macOS
author: elamalani
ms.author: emalani
ms.date: 04/23/2020
ms.topic: article
ms.assetid: b2edce7d-f0d6-4716-9a34-32da1ff2bc2d
ms.service: vs-appcenter
ms.tgt_pltfrm: ios
dev_langs:
 - swift
 - objc
---

# Migrate from the HockeySDK to App Center SDK for macOS

[!INCLUDE [sdk migration dropdown](includes/sdk-migration-dropdown.md)]

## 1. Update the libraries

### 1.1 Remove old HockeySDK

#### Manual

If you have added the SDK manually, follow these steps:

1. Remove `HockeySDK.framework` reference from the XCode project. Do not click _Move to Trash_ button, just click the _Remove reference_ button.

1. Open your project settings and under **Build Settings** tab in the **Header Search Paths** / **Framework Search Paths** sections, remove the locations for header files related to HockeySDK.

1. Open your project settings and under **Build Phases** tab in the **Link Binary with Libraries** section, remove the dependency entries related to HockeySDK.

1. Delete `HockeySDK.framework` from the file system.

#### Podfile

If you have added the SDK using **CocoaPods**, remove the `pod "HockeySDK-Mac"` line from the Podfile, then run `pod install`.

#### Carthage

If you have added the SDK using **Carthage**, follow these steps:

1. Remove HockeySDK references from the `Cartfile`. Delete `Cartfile.Resolved` from the file system.

1. Delete `HockeySDK.framework` and `HockeySDK.framework.dSYM` from the XCode project and click the **Move to Trash** button.

### 1.2 Add the new App Center SDK

The App Center SDK supports to be integrated via Cocoapods, Carthage, Swift Package Manager and using the Frameworks in your Xcode project. Read detailed instructions how to how to integrate the App Center SDK in the [App Center SDK documentation](~/sdk/getting-started/macos.md#3-add-the-app-center-sdk-modules).

> [!NOTE]
> The App Center SDK is designed with a modular approach so you can use any or all of the services. See the equivalences in the next table:
> 
> HockeyApp class  | App Center module
> ---------------- | -----------------
> `MetricsManager` | `Analytics`
> `CrashManager`   | `Crashes`

## 2. Update the SDK setup code

### 2.1 Convert the application identifier

The App Center SDK uses application identifiers in the globally unique identifier (GUID) format. Your HockeyApp App ID can be used by App Center but you need to convert it to a different format. To convert the identifier you must add four hyphens to get an 8-4-4-4-12 representation.

Before (HockeyApp):
`00112233445566778899aabbccddeeff`

After (App Center):
`00112233-4455-6677-8899-aabbccddeeff`

### 2.2 Replace SDK initialization in the application code

1. Replace the following imports in your **AppDelegate** class.

   Before:

   ```objc
   @import HockeySDK;
   ```
   ```swift
   import HockeySDK
   ```

   After:

   ```objc
   @import AppCenter;
   @import AppCenterAnalytics;
   @import AppCenterCrashes;
   ```
   ```swift
   import AppCenter
   import AppCenterAnalytics
   import AppCenterCrashes
   ```

1. Replace registration code

   Find the `didFinishLaunchingWithOptions` method and replace HockeyApp code occurrences.

   Before:

   ```objc
   [[BITHockeyManager sharedHockeyManager] configureWithIdentifier:@"APP_IDENTIFIER"];
   [[BITHockeyManager sharedHockeyManager] startManager];
   ```
   ```swift
   BITHockeyManager.shared().configure(withIdentifier: "APP_IDENTIFIER")
   BITHockeyManager.shared().start()
   ```

   After:

   ```objc
   [MSAppCenter start:@"{Your app secret}" withServices:@[[MSAnalytics class], [MSCrashes class]]];
   ```
   ```swift
   MSAppCenter.start("{Your app secret}", withServices: [MSAnalytics.self, MSCrashes.self])
   ```

1. Replace HockeyApp API calls throughout the application. The detailed API mapping tables are given below.

## 3. Services and feature comparison

### Core

#### [Objective C](#tab/objc/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Adjust log level | [[BITHockeyManager sharedHockeyManager].logLevel = BITLogLevelVerbose](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-7-debug-information) | [[MSAppCenter setLogLevel:MSLogLevelVerbose]](~/sdk/other-apis/macos.md#adjust-the-log-level)

#### [Swift](#tab/swift/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Adjust log level | [BITHockeyManager.shared().logLevel = BITLogLevel.verbose](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-7-debug-information) | [MSAppCenter.setLogLevel(MSLogLevel.Verbose)](~/sdk/other-apis/macos.md#adjust-the-log-level)

* * *

### Analytics

The HockeySDK collects metrics per default.
The App Center SDK does not collect any (analytics) metrics per default. To collect metrics using the App Center SDK, pass `MSAnalytics` to the `start:` method.

#### [Objective C](#tab/objc/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Automatically track sessions | [Documentation (enabled by default)](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-user-metrics) | [Documentation (enabled by default)](~/sdk/analytics/macos.md#session-and-device-information)
Custom events with properties| [Documentation](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-1-custom-events) | [[MSAnalytics trackEvent:withProperties:]](~/sdk/analytics/macos.md#custom-events)
Disable service at runtime | [[BITHockeyManager sharedHockeyManager].disableMetricsManager = YES](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-user-metrics) | [[MSAnalytics setEnabled:NO]](~/sdk/analytics/macos.md#enable-or-disable-app-center-analytics-at-runtime)

#### [Swift](#tab/swift/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Automatically track sessions | [Documentation (enabled by default)](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-user-metrics) | [Documentation (enabled by default)](~/sdk/analytics/macos.md#session-and-device-information)
Custom events with properties | [Documentation](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-1-custom-events) | [MSAnalytics.trackEvent(eventName, withProperties: properties)](~/sdk/analytics/macos.md#custom-events)
Disable service at runtime | [BITHockeyManager.shared().isMetricsManagerDisabled = true](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-5-user-metrics) | [MSAnalytics.setEnabled(false)](~/sdk/analytics/macos.md#enable-or-disable-app-center-analytics-at-runtime)

* * *

### Crashes

The HockeySDK reports crashes per default. Crashes will be immediately sent to the server the next time the app is launched.
The App Center SDK does not report any crashes per default. To collect crashes using the App Center SDK, pass `MSCrashes` to the `start:` method.

#### [Objective C](#tab/objc/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Automatically send crashes | [[[BITHockeyManager sharedHockeyManager].crashManager setAutoSubmitCrashReport: YES]](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-2-autosend-crash-reports) | [Documentation (enabled by default)](~/sdk/crashes/macos.md#should-the-crash-be-processed)
Generate a test crash | `[[BITHockeyManager sharedHockeyManager].crashManager generateTestCrash]` | [[MSCrashes generateTestCrash]](~/sdk/crashes/macos.md#generate-a-test-crash)
Info about the previous crash | `[[BITHockeyManager sharedHockeyManager].crashManager lastSessionCrashDetails]` | [[MSCrashes lastSessionCrashReport]](~/sdk/crashes/macos.md#details-about-the-last-crash)
Mach exception handling | `[[BITHockeyManager sharedHockeyManager].disableMachExceptionHandler = YES]` | [Documentation (enabled by default)](~/sdk/crashes/macos.md#disabling-mach-exception-handling)
Attach additional meta data | [Documentation](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-4-attach-additional-data) | [Documentation (can be attached from delegate)](~/sdk/crashes/macos.md#add-attachments-to-a-crash-report) |
Customize user dialog | [setCrashReportUIHandler](https://github.com/bitstadium/HockeySDK-Mac/blob/5.1.1/Classes/CrashReporting/BITCrashManager.h#L218-L236) | [Documentation (not provided by default)](~/sdk/crashes/macos.md#ask-for-the-users-consent-to-send-a-crash-log)
Disable service at runtime | [[[BITHockeyManager sharedHockeyManager] setDisableCrashManager: YES]](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-1-disable-crash-reporting) | [[MSCrashes setEnabled:NO]](~/sdk/crashes/macos.md#enable-or-disable-app-center-crashes-at-runtime)

#### [Swift](#tab/swift/)

Feature | HockeyApp | App Center
------- | ---------- | ---
Automatically send Crashes | [BITHockeyManager.shared().crashManager.isAutoSubmitCrashReport = true](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-2-autosend-crash-reports) | [Documentation (enabled by default)](~/sdk/crashes/macos.md#should-the-crash-be-processed)
Generate a test crash | `BITHockeyManager.shared().crashManager.generateTestCrash()` | [MSCrashes.generateTestCrash()](~/sdk/crashes/macos.md#generate-a-test-crash)
Info about the previous crash | `BITHockeyManager.shared().crashManager.lastSessionCrashDetails` | [MSCrashes.lastSessionCrashReport()](~/sdk/crashes/macos.md#details-about-the-last-crash)
Mach exception handling | `BITHockeyManager.shared().disableMachExceptionHandler = true]` | [Documentation (enabled by default)](~/sdk/crashes/macos.md#disabling-mach-exception-handling)
Attach additional meta data | [Documentation](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-4-attach-additional-data) | [Documentation (can be attached from delegate)](~/sdk/crashes/macos.md#add-attachments-to-a-crash-report) |
Customize user dialog | [setCrashReportUIHandler](https://github.com/bitstadium/HockeySDK-Mac/blob/5.1.1/Classes/CrashReporting/BITCrashManager.h#L218-L236) | [Documentation (not provided by default)](~/sdk/crashes/macos.md#ask-for-the-users-consent-to-send-a-crash-log)
Disable service at runtime | [BITHockeyManager.shared().isCrashManagerDisabled = true](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-macos#3-3-1-disable-crash-reporting) | [MSCrashes.setEnabled(false)](~/sdk/crashes/macos.md#enable-or-disable-app-center-crashes-at-runtime)

### Feedback

The feedback service won't be supported in App Center. See [HockeyApp feedback](feedback.md).
