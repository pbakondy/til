# How to implement Apache Tomcat session support with Objective-C

For complete code see this [Gist](https://gist.github.com/pbakondy/f89f44913ff57d36d8d72f455da40e40).

## Simple HTTP Request

```objc
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalAndRemoteCacheData timeoutInterval:30];

[request setHTTPMethod:httpMethod];

NSURLResponse *urlResponse = nil;

NSData *response = [self sendSynchronousRequest:request returningResponse:&urlResponse error:error];

NSInteger statusCode = [(NSHTTPURLResponse *)urlResponse statusCode];

if (statusCode >= 200 && statusCode < 300) {
    NSLog(@"Response: %@", response);
}
```

## HTTP Request with Tomcat session support

HTTP requests are stateless. Apache Tomcat provides a solution for creating and keeping a session using Cookies.

When the session starts (first request from a client) Tomcat server sends a new HTTP response header: `Set-Cookie JSESSIONID=value`.

The client saves the `JSESSIONID` cookie value and sends it along with future requests. Browsers do this behavior out-of-the-box. 

If we want to implement this feature we need to save and send `JSESSIONID` value.

```objc
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalAndRemoteCacheData timeoutInterval:30];

[request setHTTPMethod:httpMethod];

if (sessionId != nil) { // saved sessionId of class instance
    // This command makes the following changes:
    // - adds a new cookie to the Cookie Storage with name JSESSIONID=sessionId
    // - this cookie will be sent with this request and every following requests too
    // - adds new HTTP request header: JSESSIONID=sessionId
    // - adds new HTTP request header: Cookie="JSESSIONID=sessionId"
    [request addValue:sessionId forHTTPHeaderField:@"JSESSIONID"];
}

NSURLResponse *urlResponse = nil;

NSData *response = [self sendSynchronousRequest:request returningResponse:&urlResponse error:error];

NSInteger statusCode = [(NSHTTPURLResponse *)urlResponse statusCode];

sessionId = [self getSessionId:urlResponse];

if (statusCode >= 200 && statusCode < 300) {
    NSLog(@"Response: %@", response);
}
```

Reading HTTP response header:

```objc
- (NSString *)getSessionId:(NSURLResponse *)urlResponse {
    NSDictionary *headers = [(NSHTTPURLResponse *)urlResponse allHeaderFields];
    NSString *setCookieHeader = [headers objectForKey:@"Set-Cookie"];
    if (setCookieHeader == nil) {
        return nil;
    }
    NSRange range = [setCookieHeader rangeOfString:@"JSESSIONID" options:NSCaseInsensitiveSearch];
    if (range.location == NSNotFound) {
        return nil;
    }
    NSUInteger startPos = range.location + @"JSESSIONID".length + 1;
    NSRange rangeEnd = [setCookieHeader rangeOfString:@";"];
    if (rangeEnd.location == NSNotFound) {
        return nil;
    }
    NSUInteger rangeLength = rangeEnd.location - startPos;
    NSString *sessionId = [setCookieHeader substringWithRange:NSMakeRange(startPos, rangeLength)];
    return sessionId;
}
```

`[NSURLSession sharedSession]` uses a common shared Cookie Storage therefore it does not allow maintaining multiple sessions.

## HTTP Request with muliple Tomcat session support

We are able to create a separated Cookie Storage for every new logical/semantical session.

To do this we need to create a new `NSURLSessionConfiguration` instance and use it at `NSURLSession` requests. 

```objc
NSString *uuid = [[NSUUID UUID] UUIDString];

NSHTTPCookieStorage *cookieStorage = [NSHTTPCookieStorage sharedCookieStorageForGroupContainerIdentifier:uuid];

cookieStorage.cookieAcceptPolicy = NSHTTPCookieAcceptPolicyAlways;

NSURLSessionConfiguration *urlSessionConfiguration = [NSURLSessionConfiguration ephemeralSessionConfiguration];

urlSessionConfiguration.HTTPCookieStorage = cookieStorage;

[...]

[[[NSURLSession sessionWithConfiguration:urlSessionConfiguration] dataTaskWithRequest:request completionHandler:^(NSData * _Nullable _data, NSURLResponse * _Nullable _response, NSError * _Nullable _error) {

    [...]

}] resume];

```