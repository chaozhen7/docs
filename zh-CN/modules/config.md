---
root: false
name: 配置
sort: 1
---

# 简介

Blade 框架的运行是依赖于配置的，每个选项都有说明，因此你可以轻松地浏览这些文档，并且熟悉这些选项配置。

# 配置

blade 目前支持 JSON、Properties 格式的配置文件解析，也可以硬编码进行配置，但是默认采用了 Properties 格式解析，用户可以通过简单的配置就可以获得很大的灵活性，实际上你使用 Blade 开发需要的配置非常少。

# 配置方式

我们创建一个基于 blade 的应用程序时，都必须创建一个初始化配置类 继承自 `Bootstrap` 类：

```java
public class App extends Bootstrap {

	@Override
	public void init(Blade blade) {}

	@Override
	public void contextInitialized(Blade blade) {}
}
``` 

`Bootstrap` 是一个抽象类，供全局初始化使用。里面有2个方法：

- `init`：初始化操作
- `contextInitialized`：系统初始化之后进行的操作，看源码你会发现在这里可以获取更多东西(但使用并不频繁)


> _**一般在启动配置类的 `init` 方法进行 路由配置、模板配置、数据库配置等操作**_

如果你是非 web 应用且使用 jetty 启动，那么 blade 默认启动端口是 9000。

# 读取配置值

```java
Blade blade = Blade.me();
String value = blade.config().get( KEY );
```

通过如上方式获取配置文件参数配置。

`Config` 支持以下方法：

```java
get(String key)
getAsInt(String key)
getAsLong(String key)
getAsBoolean(String key)
getAsDouble(String key)
getAsFloat(String key)
```

## 系统配置参数

- `blade.basepackage`：配置项目运行的基础包
- `blade.ioc`：配置要进行ioc管理的包名，多个用 `，` 逗号隔开
- `blade.prefix`：视图目录前缀，如 `/WEB-INF/views/`，要以 `/` 开头和结尾哟
- `blade.suffix`：视图文件的后缀名，默认是 `.jsp`，你可以修改为其他
- `blade.filter_folder`：静态文件目录，在请求时这些目录会被过滤掉
- `blade.route`：配置注解路由的包名，多个用逗号隔开
- `blade.interceptor`：配置拦截器的包名，多个用逗号隔开（不推荐此方式）
- `blade.view404`：配置404模板文件位置
- `blade.view500`：配置500模板文件位置
- `blade.enableXSS`：配置是否启用XSS防御，默认不启用

其实配置很简单，你需要什么就加入什么，组件式开发真的so easy~

接下来去探寻 [路由](./route) 的秘密！