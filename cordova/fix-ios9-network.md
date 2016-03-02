# Fix iOS 9 network connectivity issue

After upgrading to Xcode7 compiling to iOS 9 your application will suffer from network connectivity problems.

The root cause of this behavior is the new `App Transport Security` rules.

You can bypass these restrictions by adding the following lines to `Info.plist` file:

```xml
<key>NSAppTransportSecurity</key>
<dict>
	<key>NSAllowsArbitraryLoads</key>
	<true/>
</dict>
```

Also you can use the following Cordova plugin: https://github.com/leecrossley/cordova-plugin-transport-security

## Further readings

- http://iosdevtips.co/post/121756573323/ios-9-xcode-7-http-connect-server-error
- http://ste.vn/2015/06/10/configuring-app-transport-security-ios-9-osx-10-11/
