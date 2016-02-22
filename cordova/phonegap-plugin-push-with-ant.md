# How to build phonegap-plugin-push with Ant

Cordova plugin [phonegap-plugin-push](https://github.com/phonegap/phonegap-plugin-push) is made to build with gradle.

Ant is not able to resolve `<framework>` tags of `plugin.xml` from Android SDK.

## Steps for Ant build

Install plugin

```
cordova plugin add phonegap-plugin-push
```

Install Android SDK resources listed in the [documentation](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md#android-details).

Copy two files to `plugins/phonegap-plugin-push/src/android/libs`:

### Copy jar files
- &lt;ANDROID_SDK&gt;/extras/android/support/v13/android-support-v13.jar
- &lt;ANDROID_SDK&gt;/extras/android/google_play_services/libproject/google-play-services_lib/libs/google-play-services.jar

### Edit `plugin.xml`

Open `plugins/phonegap-plugin-push/plugin.xml` for editing.

Remove `<framework>` tags under section `<platform name="android">`.

Add these items:

```xml
<source-file src="src/android/libs/android-support-v13.jar" target-dir="libs/" />
<source-file src="src/android/libs/google-play-services.jar" target-dir="libs/" />
```

Save and close `plugin.xml`.

## How to build with Ant

```bash
$ cordova build android -- --ant
```

or

```bash
$ export ANDROID_BUILD=ant
$ cordova build android
```
