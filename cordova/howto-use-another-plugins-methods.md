# How to use another plugins methods

Problem: I have two cordova plugins. I want to use a public method of the first plugin from the second plugin.

## Solution

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
