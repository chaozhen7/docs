---
root: false
name: Cross-Site Request Forgery
sort: 8
---

# Cross-site Request Forgery

## 一、Introduction 

CSRF (Cross-site Request Forgery): Cross-site forged.Harm is an attacker can steal your identity, in the name of your sending malicious requests.Can steal your account, for example, as you send mail, to purchase goods, etc.

## 二、Principle

The detailed schematic diagram is as follow:

![](https://i.imgur.com/VAKjlI1.jpg)

Even more terrorist use such as img tags, users don't even need to click on a link can attack, such as site B can add the following code:

```html
<img src='http://www.example.com/action?k1=v1&k2=v2' width=0 height=0 />
```

Width=0 height=0 here said the picture is not visible.This statement will cause the browser to another server sends a request.Browser whether or not the image url actually point to a picture, as long as the SRC url specified in the field, will trigger the request in accordance with the address.(browser default is not forbidden to download pictures, this is because the disable images after most of the web application availability will fall).Don't consider the image involved for the image location (can cross domain).If A website accidentally provides the get interface is unfortunately have to inform.

## Blade generation and validation CSRF token

Instead of using the CSRF protection functions as a built-in, blade configuration of the users themselves, wrote in the interceptor:

```java
blade.before("/*", (req, res) -> {
    if (req.method().equals(HttpMethod.POST)) {
        if (!CSRFTokenManager.verifyAsForm(req, res)) {
            res.text("csrf error!!!");
            req.abort();
            return;
        }
        System.out.println("post request");
    }
});
```

Route Code:

```java
blade.post("/login", (req, res) -> {
	System.out.println("go login");
});
```

Client:

```html
<form action="/protected" method="post">
    <input type="hidden" name="_csrf" value="${csrf_token}">
    <button>Submit</button>
</form>
```

## Custom Setting

The service allows accepts a parameter for the custom option, call CSRF token manager `config` method:

```java
CSRFConfig conf = new CSRFConfig();
conf.setSecret("YOUR SALT");
CSRFTokenManager.config(conf);
```

```java
// Global secret key is used to generate the token, the default for the random string
String secret = "blade";

// Used to hold the user ID of the session name, default is "csrf_token"
String session = "csrf_token";

// Used to pass the token of the HTTP request header fields, default is "X-CSRFToken"
String header = "X-CSRFToken";

// Used to pass the token of the form field name, default is "_csrf"
String form = "_csrf";

// In the name of the Cookie used to pass the token, default is "_csrf"
String cookie = "_csrf";

// Cookie set path, default is "/"
String cookiePath = "/";

// The length of the generated token, 32 bit by default
int length = 32;

// The cookies expire time, 60 seconds by default
int expire = 3600;

// Is used to specify whether to ask only setting cookies use HTTPS, default is false
boolean secured = false;

// Is used to specify whether the token is set to the response headers, default is false
boolean setHeader = false;

// Is used to specify whether the token is set to the response of the Cookie, default is false
boolean setCookie = false;
```
