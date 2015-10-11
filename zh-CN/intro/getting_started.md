---
name: 开始使用
---

# 开始使用 Blade

在我们开始之前，必须明确的一点就是，文档不会教授您任何有关 Java 语言的基础知识。所有对 Blade 使用的讲解均是基于您已有的知识基础上展开的。

## 安装Blade

添加依赖到项目中非常简单，只要确保你的版本是最新的。
mvnrepository是 [最新稳定版本](https://github.com/biezhi/blade/blob/master/LAST_VERSION.md) 

下面是一个实例：

```xml
<dependency>
    <groupId>com.bladejava</groupId>
    <artifactId>blade-core</artifactId>
    <version>x.x.x</version>
</dependency>
```

这里添加了blade的核心库，根据你的需求可以添加其他依赖。
比如模版引擎的依赖、数据库操作的依赖等。

## 不使用maven
有些同学可能还不会使用maven构建项目，建议有时间的话学习一下。

不使用maven构建基于blade的项目也非常简单，只要把你需要的jar包加进去就可以了。

`blade`核心库文件有2个 

+ [blade-kit.jar](http://mvnrepository.com/artifact/com.bladejava/blade-kit)
+ [blade-core.jar](http://mvnrepository.com/artifact/com.bladejava/blade-core) 

升级只需更换响应的jar包即可。

## 最简示例

创建一个 Maven 项目(非web项目)，在 `pom.xml` 中添加 Blade 依赖：

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
		<version>3.1.0</version>
	</dependency>
	<dependency>
		<groupId>org.eclipse.jetty</groupId>
		<artifactId>jetty-servlet</artifactId>
		<version>9.3.4.v20151007</version>
	</dependency>
</dependencies>
```

创建启动类继承自 `Bootstrap`：

```java
/**
 * Hello Blade!
 */
public class App extends Bootstrap {

	@Override
	public void init() {}
	
	public static void main(String[] args) throws Exception {
		Blade blade = Blade.me();
		blade.get("/", new RouteHandler() {
			public Object handler(Request request, Response response) {
				response.html("<h1>Hello Blade！</h1>");
				return null;
			}
		});
		
		blade.app(App.class).start();
	}
}
```

使用 `Blade.me()` 方法返回 Blade 的单例对象，在整个app中是唯一的。

方法 `blade.get` 注册一个 `/` 的路由，Blade 的默认端口是9000，运行 `main` 方法。

您应该在程序启动后看到一条日志信息：

```sh
[INFO] Blade listening on http://127.0.0.1:9000
```

现在，打开您的浏览器然后访问 [http://localhost:9000](http://localhost:9000)。您会发现，一切是如此的美好！

## Web开发示例

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
		<version>3.1.0</version>
	</dependency>
</dependencies>
```

```xml
<filter>
    <filter-name>CoreFilter</filter-name>
    <filter-class>com.blade.CoreFilter</filter-class>
    <init-param>
        <param-name>bootstrapClass</param-name>
        <param-value>blade.hello.App</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CoreFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

创建启动类：

```java
public class App extends Bootstrap {

    @Override
    public void init() {
    	Blade blade = Blade.me();

        blade.get("/", new RouteHandler() {
            public Object handler(Request request, Response response) {
                request.attr("name", "blade");
                return "index";
            }
        });
    }
}
```

这里 `return` 的 `index` 会查找 `WEB-INF/index.jsp` 文件。

将程序部署在 `Tomcat` 中运行后，打开浏览器，输入 http://127.0.0.1:8080/hello/

## 了解更多

您现在已经知道怎么基于 Blade 来书写简单的代码，请尝试修改上文中的两个示例，并确保您已经完全理解上文中的所有内容。

当您觉得自己已经原地满血复活后，就可以继续学习之后的内容了。