---
root: false
name: 跨域攻击（CSRF）
sort: 7
---

# 跨域请求攻击（CSRF）

## 一、简介

CSRF (Cross-site Request Forgery)，中文名称：跨站伪造。危害是攻击者可以盗用你的身份，以你的名义发送恶意请求。比如可以盗取你的账号，以你的身份发送邮件，购买商品等。

## 二、原理

具体的原理图如下：

![](https://i.imgur.com/VAKjlI1.jpg)

更加恐怖的是使用诸如img之类的标签，甚至不需要用户点击某个链接就可以发起攻击，比如B网站可以添加如下代码：

```html
<img src='http://www.example.com/action?k1=v1&k2=v2' width=0 height=0 />
```

这里width=0 height=0 表示图片是不可见的。这个语句会导致游览器向另外的服务器发送一个请求。游览器不管该图片url实际是否指向一张图片，只要src字段中规定了url，就会按照地址触发这个请求。（游览器默认都是没有禁止下载图片，这是因为禁用图片后大多数web程序的可用性就会打折扣）。加载图片根本不考虑所涉及的图像所在位置（可以跨域）。如果A网站不小心提供了get接口就非常不幸得中招了

## Blade生成和验证 CSRF 令牌

blade 没有把CSRF防范功能作为内置，而是用户自己配置，在拦截器里这样写：

```java
blade.before("/*").run( (req,res) -> {
			
	HttpMethod httpMethod = req.method();
	
	if(httpMethod.equals(HttpMethod.POST)){
    	if(!CSRFTokenManager.verifyAsForm(req, res)){
    		res.text("csrf error!!!");
    		return false;
    	}
    	System.out.println("post 请求");
    }	
	return req.invoke();
});
```

路由代码：

```java
blade.post("/login").run( (req,res) -> {
	System.out.println("进入login");
	return "login";
});
```

客户端：

```html
<form action="/protected" method="post">
    <input type="hidden" name="_csrf" value="${csrf_token}">
    <button>提交</button>
</form>
```

## 自定义选项

该服务允许接受一个参数来进行自定义选项，调用CSRF token管理器的 `config` 方法：

```java
CSRFConfig conf = new CSRFConfig();
conf.setSecret("你的盐值");
CSRFTokenManager.config(conf);
```

```java
// 用于生成令牌的全局秘钥，默认为随机字符串
String secret = "blade";

// 用于保存用户 ID 的 session 名称，默认为 "csrf_token"
String session = "csrf_token";

// 用于传递令牌的 HTTP 请求头信息字段，默认为 "X-CSRFToken"
String header = "X-CSRFToken";

// 用于传递令牌的表单字段名，默认为 "_csrf"
String form = "_csrf";

// 用于传递令牌的 Cookie 名称，默认为 "_csrf"
String cookie = "_csrf";

// Cookie 设置路径，默认为 "/"
String cookiePath = "/";

// 生成token的长度，默认32位
int length = 32;

// cookie过期时长，默认60秒
int expire = 3600;

// 用于指定是否要求只有使用 HTTPS 时才设置 Cookie，默认为 false
boolean secured = false;

// 用于指定是否将令牌设置到响应的头信息中，默认为 false
boolean setHeader = false;

// 用于指定是否将令牌设置到响应的 Cookie 中，默认为 false
boolean setCookie = false;
```
