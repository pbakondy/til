# Android Debug Build Signing

## Problem

Two developers work on the same Android project. They build debug apk files sometimes to install it on testing devices.

An installed app by the first developer is not upgradeable with the apk file from the second developer. The android logcat contains this message:

```
PackageManager: Package com.company.testing signatures do not match the previously installed version; ignoring!
```

Reason: every workstation uses an own debug signing certificate.

## Solution

Use the same signing certificate.

The file is located here: `~/.android/debug.keystore`

Copy this file from one's machine and use it everywhere.

## Further reading

- https://developer.android.com/studio/publish/app-signing.html#debug-mode
- http://stackoverflow.com/a/10981870
