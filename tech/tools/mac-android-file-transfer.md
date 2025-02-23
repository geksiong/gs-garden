---
title: Transferring files between Mac and Android
description: Options for Mac-Android file transfers
date: 2025-02-23

tags:
  - android
  - mac

---

## RIP Android File Transfer

I realised that Google in their infinite wisdom has decided to kill Android File Transfer, so now I need another way to transfer files between my Mac and Android.

## Options

### MacDroid

Everyone seems to be recommending MacDroid, however it is free only for Android-to-Mac transfers. My primary use case is Mac-to-Android, so no go.

### AirDroid

Needs a login account, so nope.

### LocalSend

Open-source app. Simple Android app and clients for Mac, Win, Linux. One device sends and the other receives, and you choose where to save. No file browsing capability, but good for quick transfer of a few files.

The developer mentions there are in-app purchases but I haven't found any crippled features yet.

Get it at https://localsend.org/

## ADB

For developers we can use ADB.

- Enable USB Debugging on the Android device
- On the Mac terminal:
  - `adb device`
  - `adb shell`
  - `adb pull` and `adb push`
