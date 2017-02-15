# How to use another plugins methods

Problem: I have two cordova plugins. I want to use a public method of the first plugin from the second plugin.

## Solution for Android platform

### First plugin

```java
package com.example;

import org.apache.cordova.CallbackContext;
import org.apache.cordova.CordovaPlugin;

import org.json.JSONArray;
import org.json.JSONException;

public class FirstPlugin extends CordovaPlugin {

  public void publicMethod() {
    // ...
  }

  @Override
  public boolean execute(String action, JSONArray args, CallbackContext callbackContext) throws JSONException {
    // ...
  }

}
```

### Second plugin


```java
package com.example;

import org.apache.cordova.CallbackContext;
import org.apache.cordova.CordovaPlugin;
import org.apache.cordova.PluginManager;

import org.json.JSONArray;
import org.json.JSONException;

import com.example.FirstPlugin;

public class SecondPlugin extends CordovaPlugin {
  FirstPlugin firstPlugin = null;

  @Override
  public boolean execute(String action, JSONArray args, CallbackContext callbackContext) throws JSONException {

    try {
      if (firstPlugin == null) {
        final PluginManager pluginManager = webView.getPluginManager();
        firstPlugin = (FirstPlugin) pluginManager.getPlugin("FirstPlugin");
      }
    } catch (Exception e) {
      e.printStackTrace();
    }

    if (action.equals("method")) {
      // ...

      if (firstPlugin != null) {
        firstPlugin.publicMethod();
      }

      // ...

      return true;
    }

    return false;
  }

}
```

## Solution for iOS platform

### First plugin

Plugin name definition in `plugin.xml`

```xml
<plugin ...>

  <platform name="ios">
    <config-file target="config.xml" parent="/*">
      <feature name="FirstPlugin">
        <param name="ios-package" value="FirstPlugin" />
        <param name="onload" value="true" />
      </feature>
    </config-file>
  </platform>

  ...

</plugin>
```

Method definitions in `FirstPlugin.h`

```objc
#import <Cordova/CDVPlugin.h>

@interface FirstPlugin : CordovaPlugin

// public cordova methods
- (void)method1:(CDVInvokedUrlCommand *)command;
- (void)method2:(CDVInvokedUrlCommand *)command;

// shared methods for another plugins
- (void)internalMethod1;
- (NSString *)internalMethod2:(NSString *)message;

@end
```

### Second plugin

Method calls in `SecondPlugin.m`

```objc
#import SecondPlugin.h
#import FirstPlugin.h

@implementation SecondPlugin

- (void)secondPluginMethod:(CDVInvokedUrlCommand *)command {

  // ...

  [[self.commandDelegate getCommandInstance:@"FirstPlugin"] internalMethod1];

  NSString *return = [[self.commandDelegate getCommandInstance:@"FirstPlugin"] internalMethod2:@"message"];

  // ...

}

@end

```
