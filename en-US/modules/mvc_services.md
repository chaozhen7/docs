---
name: MVC服务
---

# MVC服务

Blade 会注入一些默认服务来驱动您的应用，这些服务被称之为 **核心服务**。也就是说，您可以直接使用它们作为处理器参数而不需要任何附加工作。

### 请求对象

该对象是 `Request` 的实例。是 Blade 中路由的核心对象，获取参数，设置参数等操作都通过 `Request` 对象操作。

我们经常需要获取用户传递的数据，包括Get、POST等方式的请求，blade里面会自动解析这些数据，你可以通过如下方式获取数据： 使用Request对象的方法

```java
request.query("");
request.queryAsInt("");
request.queryAsDouble("");
request.queryAsFloat("");
request.queryAsBoolean("");
request.queryAsLong("");
```

使用例子如下：

```java
// hi请求
public void hi(Request request){
    System.out.println(request.query("name"));
}
```

更多其他的request的信息，用户可以通过request获取信息，关于该对象的属性和方法参考手册Request。

**获取path上的参数**

在REST风格的URL路径上，我们的配置是类似于 `/user/:uid` 酱紫的。 那么如何获取呢？blade的request对象提供了如下方法，使用很简单

```java
request.param("");
request.paramAsInt("");
request.paramAsLong("");
```

**获取 Request Body 里的内容**

在 API 的开发中，我们经常会用到 `JSON` 或 `XML` 来作为数据交互的格式，如何在blade中获取Request Body里的JSON或XML的数据呢？

```java
String body = request.body().asString;
```

**文件上传**

blade默认支持对表单文件上传的解析，就是别忘记在你的form表单中增加这个属性 `enctype="multipart/form-data"` ，否则你的浏览器不会传输你的上传文件。 文件上传之后一般是放在系统的内存里面，如果文件的size大于设置的缓存内存大小，那么就放在临时文件中，默认的缓存内存是1MB·，你可以通过如下来调整这个缓存内存大小:

```java
ServletFileUpload fileUpload = ServletFileUpload.parseRequest(request.servletRequest());
// 判断是否是表单上传类型
boolean isMultipart = fileUpload.isMultipartContent(request.servletRequest());
if(isMultipart){
    // 10MB
    int buf_size = 10 * 1024 * 1024;
    fileUpload.setBufferSize(buf_size);
}
```

blade提供获取表单上传文件的方法 `FileItem fileItem = fileUpload.fileItem("上传Input的name")` 下面是一个上传文件的示例：

```java
/**
 * 上传图片
 */
@Route(value="uploadimg", method = HttpMethod.POST)
public void uploadImg(Request request, Response response){
    ServletFileUpload fileUpload = 
       ServletFileUpload.parseRequest(request.servletRequest());
    boolean isMultipart = fileUpload.isMultipartContent(request.servletRequest());
    if(isMultipart){
        FileItem fileItem = fileUpload.fileItem("image");
        String suffix = FileKit.getExtension(fileItem.getFileName());
        if(StringKit.isNotBlank(suffix)){
            suffix = "." + suffix;
        }

        String saveName = DateKit.dateFormat(new Date(), "yyyyMMddHHmmssSSS") 
                       + "_" + StringKit.getRandomChar(10) + suffix;
        String fname = Blade.webRoot() + File.separator + 
                       Constant.UPLOAD_FOLDER + File.separator + saveName;
        File file = new File(fname );

        IOKit.write(fileItem.getFileContent(), file);

        JSONObject jsonObject = new JSONObject();

        if(file.exists()){

            String prfix = request.url().replaceFirst(request.servletPath(), "/");
            String filePath = Constant.UPLOAD_FOLDER + "/" + saveName;
            String url = prfix + filePath;

            jsonObject.put("id", saveName);
            jsonObject.put("url", url);
            jsonObject.put("success", 1);
            jsonObject.put("message", "上传成功");

        } else {
            jsonObject.put("success", 0);
            jsonObject.put("message", "上传失败");
        }   
        response.json(jsonObject.toString());
    }
}
```

### 响应流

该对象是 `Response` 的实例。是 Blade 中路由的核心对象，输出数据，跳转等操作都通过 `Response` 对象操作。

使用方法：

```java
public void index(Response res){
	res.html("<h1>hello</h1>");
	res.text("<h1>hello</h1>");
	res.json("{'name':'jack'}");
}
```

## 请求上下文（Context）

该服务通过 `BladeWebContext` 来体现，它可以获取到当前请求的 `Request` 和 `Response`。
使用方法：

```java
public void index(){
	Request req = BladeWebContext.request();
	...
}
```

### Cookie

最基本的 Cookie 用法：

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

使用以下顺序的参数来设置更多的属性：`cookie(name, value, maxAge, secured);`。

因此，设置 Cookie 最完整的用法为：`cookie("user", "unknwon", 999, true)`。

需要注意的是，参数的顺序是固定的。

对于那些对安全性要求特别高的应用，可以为每次设置 Cookie 使用不同的密钥加密/解密。

## 静态文件

该服务可以通过函数 `staticFolder("","");` 来设置。该服务主要负责应用静态资源的服务，当您的应用拥有多个静态目录时，可以对其进行多个设置。

使用方法：

```java
public void init(){
	Blade blade = Blade.me();
	blade.staticFolder("/public", "/assets");
}
```