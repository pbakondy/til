# Base64 encoding & decoding

- https://en.wikipedia.org/wiki/Base64

## JavaScript #1

```js
function base64encode(str) {
    return window.btoa(unescape(encodeURIComponent(str)));
}

function base64encodeURLSafe(str) {
    let encoded = base64encode(str);
    return encoded.replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');
}

function base64decode(str) {
    return decodeURIComponent(escape(window.atob(str)));
}
```

- https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/btoa
- http://caniuse.com/#feat=atob-btoa

## JavaScript #2

Use base64.js ( `js-base64` ) npm package

```
$ npm install --save js-base64
```

- https://github.com/dankogai/js-base64

## Java

```java
import org.apache.commons.codec.binary.Base64;
static final String UTF8 = "UTF-8";

String base64encode(String str) throws UnsupportedEncodingException {
    return Base64.encodeBase64String(str.getBytes(UTF8));
}

String base64encodeURLSafe(String str) throws UnsupportedEncodingException {
    return Base64.encodeBase64URLSafeString(str.getBytes(UTF8));
}

String base64decode(String str) throws UnsupportedEncodingException {
    return new String(Base64.decodeBase64(str), UTF8);
}

```

- https://commons.apache.org/proper/commons-codec/apidocs/org/apache/commons/codec/binary/Base64.html

## Android

```java
import android.util.Base64;
static final String UTF8 = "UTF-8";

String base64encode(String str) throws UnsupportedEncodingException {
    return Base64.encodeToString(str.getBytes(UTF8), Base64.DEFAULT);
}

String base64encodeURLSafe(String str) throws UnsupportedEncodingException {
    return Base64.encodeToString(str.getBytes(UTF8), Base64.URL_SAFE);
}

String base64decode(String str) throws UnsupportedEncodingException {
    return new String(Base64.decode(str.getBytes(UTF8), Base64.DEFAULT), UTF8);
}

```

- https://developer.android.com/reference/android/util/Base64.html

## Objective-C

```objc
- (NSString *)base64encode:(NSString *)str {
    NSData *data = [str dataUsingEncoding:NSUTF8StringEncoding];
    data = [data base64EncodedDataWithOptions:0];
    return [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
}

- (NSString *)base64encodeURLSafe:(NSString *)str {
    NSString *encoded = [self base64encode:str];
    encoded = [encoded stringByReplacingOccurrencesOfString:@"+" withString:@"-"];
    encoded = [encoded stringByReplacingOccurrencesOfString:@"/" withString:@"_"];
    encoded = [encoded stringByReplacingOccurrencesOfString:@"=" withString:@""];
    return encoded;
}

- (NSString *)base64decode:(NSString *)str {
    NSData *data = [[NSData alloc] initWithBase64EncodedString:str options:NSDataBase64DecodingIgnoreUnknownCharacters];
    return [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
}
```
