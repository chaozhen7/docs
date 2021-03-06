---
root: false
name: 拦截器
sort: 3
---

# 简介

Blade 拦截器提供一个方便的机制来过滤进入应用程序的 HTTP 请求，例如，当一个用户没有登录的时候，我们判读用户登录 `Session` 是否存在，如果用户登录 `Session` 不存在，拦截器会将用户导向登录页面，然而，如果用户登录 `Session` 存在，拦截器将会允许这个请求进一步继续前进。

当然，除了身份验证之外，拦截器也可以被用来执行各式各样的任务，比如在所有请求中添加版本号、在请求返回的时候加入头信息等。

# 创建拦截器

创建拦截器的方式非常简单，blade 的做法是像创建一个路由一样创建拦截器，不过请求类型是 `Before` Or `After`。像这样：

```java
blade.before("/*", (req,res) -> {
    System.out.println("before 请求");
});
```

# 默认拦截器包

我们在前面设置了 `basepackage` , Blade 默认会设置 `basepackage.interceptor` 包为拦截器包。
在这里你可以创建自定义的拦截器，像这样：

```java
@Intercept
public class BaseInterceptor implements Interceptor {

	@Override
	public boolean before(Request request, Response response) {
		request.attribute("base", request.contextPath());
		return true;
	}

	@Override
	public boolean after(Request request, Response response) {
		return true;
	}
}
```

这个拦截器会拦截所有的路由请求，而且会在request作用域设置一个 `base` 的变量。

相同的也支持，注解方式方式注册！