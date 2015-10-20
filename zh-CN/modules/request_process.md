---
root: false
name: 请求数据处理
sort: 3
---

# 请求数据处理

## 请求数据接收

blade 静默的在所有的路由上加入了2个参数对象，分别是 `Request` 和 `Response` 对象。

### 获取参数

我们经常需要获取用户传递的数据，包括Get、POST等方式的请求，blade里面会自动解析这些数据，你可以通过如下方式获取数据：
使用 `Request` 对象的方法

```java
request.query("");
request.queryToInt("");
request.queryToDouble("");
request.queryToFloat("");
request.queryToBoolean("");
request.queryToLong("");
```

使用例子如下：

```java
// hi请求
public void hi(Request request){
    System.out.println(request.query("name"));
}
```

更多其他的 `request` 的信息，用户可以通过`request`获取信息，关于该对象的属性和方法参考手册[Request](#)。

### 获取 `/user/:uid` 的参数

在REST风格的URL路径上，我们的配置是类似于`/user/:uid`酱紫的。
那么如何获取呢？blade的request对象提供了如下方法，使用很简单

```java
request.param("");
request.paramToInt("");
request.paramToLong("");
```

###获取 Request Body 里的内容
在 API 的开发中，我们经常会用到`JSON`或`XML`来作为数据交互的格式，如何在`blade`中获取`Request Body`里的`JSON`或`XML`的数据呢？
```java
String body = request.body();
```

## 返回数据给客户端


按照数据输出我们有这么几种，以前是把数据放在`request`域中的，后面流行的`JSON`数据、`XML`数据等格式返回。
`blade`当初设计的时候就考虑了`API`功能的设计，`Response`提供了这样的方式直接输出：
**将数据存在Request域返回**
```java
public String index(Request request){
    request.attribute("name", "jack");
    return "index";
}
```
**JSON方式返回**
```java
public void getUser(Response response){
	JSONObject data = new JSONObject();
	data.put("status", 200);
	data.put("data", Map / List);
	
	response.json(data.toString());
}
```
`blade`提供了对`JSON`的处理工具类`JSONKit`
**XML方式返回**
```java
public void getUser(Response response){
    String xml = "xml内容";
    response.xml(xml);
}
```
`blade`后期会考虑直接将对象转为`xml`数据格式，当前版本还不支持。

### session处理

绝大部分的用户系统都会用到`session`来保存用户登录状态，在`blade`中使用起来极为方便。
```java
User user = userService.login(login_name, pass_word);
if(null != user){
    request.session().attribute(Constant.LOGIN_SESSION, user);
} else {
    modelAndView.add(this.ERROR, "用户名或密码错误");
}
```
这个代码片段是用户登录后将用户对象存在`session`中。
```java
User login_user = request.session().attribute(Constant.LOGIN_SESSION);
// 未登录
if(null == login_user && request.uri().indexOf("/admin") != -1){
    response.redirect("/signin");
    return;
}
```
拦截器中获取用户对象，如果session中没有则跳转到登录页。

## 处理上传

blade默认支持对表单文件上传的解析，就是别忘记在你的`form`表单中增加这个属性`enctype="multipart/form-data"`，否则你的浏览器不会传输你的上传文件。
文件上传之后一般是放在系统的内存里面，如果文件的`size`大于设置的缓存内存大小，那么就放在临时文件中，默认的缓存内存是`1MB·`，你可以通过如下来调整这个缓存内存大小:
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
`blade`提供获取表单上传文件的方法
`FileItem fileItem = fileUpload.fileItem("上传Input的name");`
下面是一个上传文件的示例：
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

## 配置拦截器

拦截器在Spring中是一个惯用的编程手段，在`blade`中也存在拦截器，功能简化一些。

```java
@Interceptor
public class BaseInterceptor {
	
	TimwMonitor monitor = TimwManager.getTimerMonitor();
	
	@Before("/*")
	public void before(Request request, Response response){
		
		System.out.println("before...");
		monitor.start();
		
		request.attribute("base", request.contextPath());
		request.attribute("static_v", "20150722");
		
		User login_user = request.session().attribute(Constant.LOGIN_SESSION);
	}
	
	@After("/*")
	public void after(){
		System.out.println("after...");
		monitor.end();
		monitor.render();
		monitor.renderAvg();
	}
}
```

在新版本中支持函数式注册拦截器，写法如下：

```java
blade.before("/*", new RouteHandler() {
	@Override
	public Object handler(Request request, Response response) {
		if(!BBSKit.isInstall() && request.uri().indexOf("/install") == -1){
			return request.invoke("/install");
		}
		request.attribute("cdn", Constant.CDN_SITE);
		request.attribute("version", "1.0");
		return request.invoke();
	}
});
```
