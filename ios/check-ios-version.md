# How to check iOS version

The latest API to check version is operatingSystemVersion method of [NSProcessInfo](https://developer.apple.com/reference/foundation/nsprocessinfo).

## Usage

```objc
// complete form
if ([[NSProcessInfo processInfo] isOperatingSystemAtLeastVersion:(NSOperatingSystemVersion){.majorVersion = 9, .minorVersion = 1, .patchVersion = 0}]) {
    NSLog(@"> iOS 9.1");
}

// compact form
if ([NSProcessInfo.processInfo isOperatingSystemAtLeastVersion:(NSOperatingSystemVersion){9,1,0}]) {
    NSLog(@"> iOS 9.1");
}
```

## Example

```objc
LAContext *laContext = [[LAContext alloc] init];
bool available;
NSError *error;

if ([NSProcessInfo.processInfo isOperatingSystemAtLeastVersion:(NSOperatingSystemVersion){9,0,0}]) {
    available = [laContext canEvaluatePolicy:LAPolicyDeviceOwnerAuthentication error:&error];
} else {
    available = [laContext canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error];
}
```

