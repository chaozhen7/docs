---
name: MVC Service
---

# MVC Service

Blade will inject some service to drive your application by default, These services are called **core services**.
That is to say, you can use them directly as processor parameters without any additional work.

### Request

The object is the Request instance.The path of the Blade is the core object, access parameters, set the parameter such as operating through the Request object operation.

We often need to Get the user data, including the way of the Get and POST Request, the blade will automatically parse the data inside, you can Get the data through the following way: use the Request object methods

```java
request.query("");
request.queryAsInt("");
request.queryAsDouble("");
request.queryAsFloat("");
request.queryAsBoolean("");
request.queryAsLong("");
```

Use examples as follow:

```java
// hi request
public void hi(Request request){
    System.out.println(request.query("name"));
}
```

More information request, the user can obtain information from the request, about the object's properties and methods [reference manual request](http://bladejava.com/apidocs/com/blade/web/http/Request.html).

**Get on the path of parameters**

On the URL path of REST style, our configuration is similar to a `/user/:uid` such.So how do you get ? Blade of the request object provides the following methods, the use of very simple:

```java
request.param("");
request.paramAsInt("");
request.paramAsLong("");
```

**The content of the access Request in the Body**

In the development of the API, we often use `JSON` or `XML` as the format of the data interaction, how to obtain the Request in blade JSON or XML data in the Body ?

```java
String body = request.body().asString;
```

**File Upload**

Blade support by default on the interpretation of the form file upload, is don't forget to add this attribute in your form form ` enctype = "multipart/form - data" `, your browser will not transfer your upload files.After the file upload is generally in the system memory, if the file size is greater than the set of cache memory size, then placed in a temporary file, the default is 1 MB cache memory, and you can adjust the cache memory size by such as down:

```java
public void someUplaod(Request request, Response response){
    FileItem[] fileItems = request.files();
    if(null != fileItems && fileItems.length > 0){
        FileItem fileItem = fileItems[0];
        System.out.println(fileItem.getFileName());
        System.out.println(fileItem.getName());
        System.out.println(fileItem.getFile());
    }
}
```

The following is an upload instances:

```java
public void uploadImage(Request request, Response response){
    FileItem[] fileItems = request.files();
    if(null != fileItems && fileItems.length > 0){
        File file = fileItem.getFile();
        // Save the file to disk
    }
}
```

### Response

The object is `Response` instance, is the core of the Blade by the middle object, output data, jump, etc all by `Response` object operation.

Usage:

```java
public void index(Request request, Response res){
	res.html("<h1>hello</h1>");
	res.text("<h1>hello</h1>");
	res.json("{'name':'jack'}");
}
```

## Request Context

The service through `BladeWebContext` to reflect, it can get to the current Request `Request` and `Response`.

Usage:

```java
public void index(){
	Request req = BladeWebContext.request();
	...
}
```

### Cookie

The most basic use cookies:

```java
//...
public void set(Response res){
	res.cookie(name, value);
}

public void get(Response res){
	res.cookie(name);
}
// ...
```

Use the following parameters to set properties for more order: `cookie(name, value, maxAge, secured);`。

Therefore, setting cookies for the most complete usage: `cookie("user", "unknwon", 999, true)`。

It is important to note that the order of the parameters are fixed.

For particularly high security requirements of applications, can be set for each Cookie use different key encryption/decryption.
