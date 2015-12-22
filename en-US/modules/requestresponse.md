---
root: false
name: Request And Response
sort: 4
---

# Request data processing

## The request data receiving

Blade silent in all routing joined the two parameter object, respectively is `Request` and `Response` object.

### Get Parameters

A we often need to Get the user access parameters transmission of data, including the way of the Get and POST request, the blade will automatically parse the data inside, you can Get the data through the following way.

Using `Request` objects method:

```java
request.query("");
request.queryAsInt("");
request.queryAsDouble("");
request.queryAsFloat("");
request.queryAsBoolean("");
request.queryAsLong("");
```

Use examples as follows:

```java
// hi request
public void hi(Request request, Response response){
    System.out.println(request.query("name"));
}
```

More other `request` information, users can `request` access to information, about the object's properties and methods of reference manual [Request](http://bladejava.com/apidocs/com/blade/web/http/Request.html)

### Get `/user/:uid` Param

On the URL path of REST style, our configuration is similar to a `/user/:uid` .
So how do you get?Blade of the request object provides the following methods, the use of very simple

```java
request.param("");
request.paramAsInt("");
request.paramAsLong("");
```

### The content of the access Request in the Body

In the development of the API, we often use `JSON` or `XML` as the format of the data interaction, how in `blade` derive Request Body in `JSON` or `XML` data ?

```java
String body = request.body().toString();
```

## Return the data to the client

According to the data output, we have so few were put data `request` domain, the popular `JSON` data, `XML` data format, such as return.

`blade` design is considered at the beginning of the API function, `Response` provides this way directly to the output:

**The data is returned Request domain**

```java
public String index(Request request, Response response){
    request.attribute("name", "jack");
    return "index";
}
```

**JSON Response**

```java
public void getUser(Request request, Response response){
    JsonObject data = new JsonObject();
    data.add("status", 200);
    data.add("data", "data");
    
    response.json(data.toString());
}
```

Blade provides for JSON processing tools `JSONKit` And `JsonObject`.

**XML Response**

```java
public void getUser(Request request, Response response){
    String xml = "xml content";
    response.xml(xml);
}
```

Later will consider the object directly into XML data format, the current version does not support。

### Session

The vast majority of the user's system can use `session` to save the state of the user login, in `blade` use rise very convenient.

```java
User user = userService.login(login_name, pass_word);
if(null != user){
    request.session().attribute(Constant.LOGIN_SESSION, user);
} else {
    modelAndView.add(this.ERROR, "username or password error!");
}
```

This snippet is user login the user object `session`.

```java
User login_user = request.session().attribute(Constant.LOGIN_SESSION);
// NO login
if(null == login_user && request.uri().indexOf("/admin") != -1){
    response.redirect("/signin");
    return;
}
```

The interceptor get the user object, if the session is no jump to the login page.

## Config Interceptor

Interceptors in Spring is a usual means of programming, in `blade` also exists in the interceptor, simplify some function.

```java
@Interceptor
public class BaseInterceptor {
    
    TimwMonitor monitor = TimwManager.getTimerMonitor();
    
    @Before("/.*")
    public void before(Request request, Response response){
        
        System.out.println("before...");
        monitor.start();
        
        request.attribute("base", request.contextPath());
        request.attribute("static_v", "20150722");
        
        User login_user = request.session().attribute(Constant.LOGIN_SESSION);
    }
    
    @After("/.*")
    public void after(){
        System.out.println("after...");
        monitor.end();
        monitor.render();
        monitor.renderAvg();
    }
}
```

Support function in new version type registered interceptors, written as follows:

```java
blade.before("/.*", new RouteHandler() {
    @Override
    public void handler(Request request, Response response) {
        request.attribute("base", request.contextPath());
        request.attribute("version", "1.0");
    }
});
```