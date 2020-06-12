---
title: App Center Build FAQs
description: Code signing apps built with App Center
keywords: build, faq
author: elamalani
ms.date: 06/08/2020
ms.topic: article
ms.assetid: bcb76094-34c6-48ae-a24d-60c14a261518
ms.service: vs-appcenter
ms.custom: build
---

# General Troubleshooting
## Why did my build fail?
There are various reasons why your build could have failed. The best way to find out what happened is to go to the build details page and download the logs. Check the logs for each of the steps to understand what happened and where exactly the build failed. If you can't understand why the build failed or you think there's an issue on our side, feel free to contact us via [App Center support](~/general/support-center.md) right from within App Center.

## Why is the build in App Center taking longer than my local build?
There are many reasons why build duration can be higher when using a build service:

* When running your build locally, many things are cached (for example NuGet packages, pods, dependencies); in App Center, we always do a clean build and redownload everything required.
* For Xamarin builds, when running your build locally, you're most likely running a simulator build; In App Center, you can run a device build (signed), which takes much longer to run.
* Most likely the CPU power of your development machine is higher than the CPU of our VMs.

We're always working on improving build times. If you consider the build duration for your app is too long compared to your expectations, contact us on the App Center website using **? > Contact Support**.

## Build Notifications
Email and custom webhook notifications give you continual real-time updates of your build status, as well as the permanency and ability to search and be directed to your builds in App Center. This also enables faster collaboration, since you can forward the build status emails or @ mention collaborators in a chat or channel in a single workflow.

You can choose to have email and webhook notifications automatically sent for the following events:

Build success:
* **Always** When your app builds successfully
* **Only** if previously failed: When your app has successfully built after one or more failed builds
* **Never** You won't receive notifications for build success
Build failure:
* **Always** When your app fails to build
* **Only if previously successful** When your app has failed to build after one or more successful builds
* **Never** You won't receive notifications for build failure

By default, email notifications are sent only when a new version is released. Whether you're an admin, app developer, or tester, it's important to be notified immediately when a new version is released to testers or to the store. For admins or app developers involved in the Continuous Integration, Delivery and Deployment process, it's useful to set up email and custom webhook notifications for build and distribute.


## Why do I get an extended build time when **Run launch test on a device** is enabled?
We run the test as part of the build operation, which gives the added build time. What happens is that while App Center Test is validating your app is ready to run on real devices, several things can happen here like: signing, checking permissions, and so on. After that it's time to wait for a device. Third, it's running the app on a phone, which takes little time. And lastly, we move test logs, screenshots into the cloud.

Expect an additional **10 minutes of build time**.

## My build scripts (Bash) doesn't execute the logic written inside it.
App Center allows you to use build scripts for some level of customization with builds. 

If you clicked "Save & Build" after modifying scripts, and you see App Center running the Build Script step, for instance, without actually executing the contents inside, chances are, it's because of the line endings.

Note that these Bash scripts are run on a Mac machine. So they're expected to have UNIX style line endings (LF).

If you're using Windows to edit/create these scripts, ensure they're saved with the Unix (LF) format. For Notepad ++, ensure from the menu, **Edit > EOL Conversion > Unix (LF)** is selected.

## When do I have to update my build configuration manually through Save & Build?
You might have noticed the handy **Save & Build** option in App Center Build Configuration. It does more than just help you trigger the build immediately after Saving/Updating changes. Generally, your app build configuration is analyzed when you open the build configuration dialog. If you have made changes to your repository that don't get picked up by the build automatically, you might need to go to the configuration page again and trigger a **Save & Build** from there.

One such example is when you add new Build Scripts to your repository. Since the build scripts are only analyzed when configuring your branch, you'll need to do another analysis to reindex your repository tree and save the results. If you simply made changes to an existing build script, which was also already used in a branch configuration, you can push your changes to the script.

We're working on improving reindexing Build Scripts when changed. Until then, a valid workaround is to do a manual **Save & Build**.

## When configuring a branch, I get an error message saying no projects can be found in my branch
App Center analyzes the contents of the branch in your repository to find an app project matching the platform selected for your app in App Center. This assumes your project uses the platform-specific standards for configuration, that is, an Xcode project or workspace for iOS apps, a Gradle project for Android apps and a solution or project for your Xamarin apps.

App Center currently only searches four directory levels deep for your project files. If App Center doesn't find your project in your branch, moving it to the root directory might help. If your repository is large, it may help to reduce its size or number of files.
