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

### 获取 `页面表单域` 对象

`request.model` 用来接受页面表单域传递过来的对象,表单域名称以 “pojoName.attrName” 方式命名，详见如下：
```java
//定义Pojo
public class Person {
  private long id;
  private String name;
  private String address;
  ...
  getter/setter 省略
}


//在页面表单中采用pojoName.attrName的形式作为表单域的name

<from action="/person/save" method="post">
  <input name="person.name" type="text">
  <input name="person.address" type="text">
  ...
</from>

public class Person {
  public void save(Request request, Response response){
     Pserson person = request.model("person", Person.class);
     ...
  }
}
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
    return "index.jsp";
}
```

**JSON方式返回**

```java
public void getUser(Request request, Response response){
	JSONObject data = new JSONObject();
    data.put("status", 200);
    data.put("data", "data");
    
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
