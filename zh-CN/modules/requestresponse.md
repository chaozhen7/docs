---
root: false
name: 请求响应
sort: 4
---

# 请求数据处理

## 请求数据接收

blade 静默的在所有的路由上加入了2个参数对象，分别是 `Request` 和 `Response` 对象。

### 获取参数

我们经常需要获取用户传递的数据，包括Get、POST等方式的请求，blade里面会自动解析这些数据，你可以通过如下方式获取数据：
使用 `Request` 对象的方法

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

更多其他的 `request` 的信息，用户可以通过`request`获取信息，关于该对象的属性和方法参考手册[Request](http://bladejava.com/apidocs/com/blade/web/http/Request.html)。

### 获取 `/user/:uid` 的参数

在REST风格的URL路径上，我们的配置是类似于`/user/:uid`酱紫的。
那么如何获取呢？blade的request对象提供了如下方法，使用很简单

```java
request.param("");
request.paramAsInt("");
request.paramAsLong("");
```

###获取 Request Body 里的内容

在 API 的开发中，我们经常会用到`JSON`或`XML`来作为数据交互的格式，如何在`blade`中获取`Request Body`里的`JSON`或`XML`的数据呢？

```java
String body = request.body().toString();
```

## 返回数据给客户端

按照数据输出我们有这么几种，以前是把数据放在`request`域中的，后面流行的`JSON`数据、`XML`数据等格式返回。
`blade` 设计之初就考虑到了 API 功能，`Response`提供了这样的方式直接输出：

**将数据存在Request域返回**

```java
public String index(Request request, Response response){
    request.attribute("name", "jack");
    return "index";
}
```

**JSON方式返回**

```java
public void getUser(Request request, Response response){
	JsonObject data = new JsonObject();
    data.add("status", 200);
    data.add("data", "data");
    
    response.json(data.toString());
}
```

`blade`提供了对`JSON`的处理工具类`JSONKit`

**XML方式返回**

```java
public void getUser(Request request, Response response){
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

## 配置拦截器

拦截器在Spring中是一个惯用的编程手段，在`blade`中也存在拦截器，功能简化一些。

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

在新版本中支持函数式注册拦截器，写法如下：

```java
blade.before("/.*", new RouteHandler() {
	@Override
	public void handler(Request request, Response response) {
		request.attribute("base", request.contextPath());
		request.attribute("version", "1.0");
	}
});
```
