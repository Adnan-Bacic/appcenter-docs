---
title: How to add Firebase SDK (Android)
description: How to add Firebase SDK (Android)
keywords: sdk, push
author: king-of-spades
ms.author: kegr
ms.date: 02/26/2021
ms.topic: article
ms.assetid: cdd0a651-29b6-4fb2-8fc5-4a25b54eb2ca
ms.tgt_pltfrm: android
---

# How to add Firebase SDK dependencies (Android)

[!INCLUDE [introduction to android](includes/introduction-android.md)]

## 1. Integrate Firebase in application

- In the [Firebase Console](https://console.firebase.google.com), go to **Project Settings**.
- Download the **google-services.json** file to your Android project app module.
- Modify the **project root** level **build.gradle** file:

    ```groovy
    buildscript {
        repositories {
            // Add google line if missing before mavenCenter
            google()
            mavenCenter()
        }

        dependencies {
            // Add this line
            classpath 'com.google.gms:google-services:4.0.1'
        }
    }

    allprojects {
        repositories {
            // Add google line if missing before mavenCenter
            google()
            mavenCenter()
        }
    }
    ```

    > [!NOTE]
    > Google introduced the `google()` repository with Gradle v4. If your Gradle version is lower than v4, then you need to use `maven { url 'https://maven.google.com' }` instead of `google()`.

- Modify the **app** level **build.gradle** file:

    ```groovy
    // Add this line at the bottom
    apply plugin: 'com.google.gms.google-services'
    ```

- Make sure to trigger a Gradle sync in Android Studio.

## 2. Remove Sender ID

Remove the `Push.setSenderId("{Your Sender ID}")` method call before `AppCenter.start`.
