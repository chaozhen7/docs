---
root: false
name: 快速入门
sort: 2
---

# 快速入门

迫不及待要开始了吗？本页提供了一个很好的 Blade 介绍，并假定你已经安装好了 Blade。如果没有，请跳转到 [开始使用](./getting_start) 章节。

## Web开发启程

创建一个 maven 的 web 项目，加入 Blade 依赖，然后配置 `web.xml`：

```xml
<dependencies>
	<dependency>
		<groupId>com.bladejava</groupId>
		<artifactId>blade-core</artifactId>
		<version>[最新版本]</version>
	</dependency>
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>javax.servlet-api</artifactId>
		<version>3.0.1</version>
	</dependency>
</dependencies>
```

```xml
<servlet>
	<servlet-name>dispatcherServlet</servlet-name>
	<servlet-class>com.blade.web.DispatcherServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
	<init-param>
		<param-name>bootstrap</param-name>
		<param-value>blade.sample.App</param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>dispatcherServlet</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

创建启动类：

```java
public class App extends Bootstrap {

    @Override
    public void init(Blade blade) {

        blade.get("/", new RouteHandler() {
            public void handle(Request request, Response response) {
                request.attr("name", "blade");
                response.render("index");
            }
        });
    }
}
```

这里渲染的 `index` 会查找 `WEB-INF/index.jsp` 文件。

将程序部署在 `Tomcat` 中运行后，打开浏览器，输入 [http://127.0.0.1:8080/hello/](http://127.0.0.1:8080/hello/)

## 做了什么？

1. 首先，我们创建了 `App` 类。这个类的是整个程序的配置启动类，所有的初始化操作都在这里完成。
2. 接下来，使用 `Blade` 的 `get` 方法创建了一个路由，返回到 `index.jsp` 视图上，传递了一个参数 `name`。
3. 将应用在 web 服务器上运行起来。

## 了解更多

您现在已经知道怎么基于 Blade 来书写简单的代码，请尝试修改上文中的示例，并确保您已经完全理解上文中的所有内容。

当您觉得自己已经原地满血复活后，就可以继续学习之后的内容了。