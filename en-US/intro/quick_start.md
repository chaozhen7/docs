---
root: false
name: Quick start
sort: 2
---

# Quick start

Can't wait to get started?This page provides a good Blade is introduced, and assume that you have installed an Blade.If not, please jump to [Get Started](./getting_start) section.

## Web development on

Create a maven web project, join Blade, then configure `web.xml`：

```xml
<dependencies>
	<dependency>
		<groupId>com.bladejava</groupId>
		<artifactId>blade-core</artifactId>
		<version>[LAST_VERSION]</version>
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

Create a launch configuration classes:

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

Here render `index` will find `WEB-INF/index.jsp` file。

Will be deployed in App `Tomcat` in running, open the browser, enter the [http://127.0.0.1:8080/hello/](http://127.0.0.1:8080/hello/)

## What to do？

1. First of all, we created a `App` class.This class is the process of configuration start class, all initialization is done here.
2. Next, using  `blade.get` method creates a routing, returns to the `index.jsp` view, pass a parameter `name`.
3. For application on a web server to run.

## Learn More

You now know how to write a simple code based on the Blade, please try to modify the example above, and ensure that you have fully understand all of the above content.

When you think that in situ with blood after his resurrection, can continue to learn after the content.