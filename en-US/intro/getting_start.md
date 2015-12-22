---
root: false
name: 开始使用
sort: 1
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

`blade` 核心库文件有2个：`blade-kit.jar` 和 `blade-core.jar`

在这里 [下载](https://github.com/biezhi/blade/releases/)。

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
		<groupId>com.bladejava</groupId>
		<artifactId>blade-startup</artifactId>
		<version>1.0.0</version>
	</dependency>
</dependencies>
```

创建一个服务类，普通 Java 类即可：

```java
/**
 * Hello Blade!
 */
public class App {
	
	public static void main(String[] args) {
		Blade blade = Blade.me();
		blade.get("/", new RouteHandler() {
			public void handle(Request request, Response response) {
				response.html("<h1>Hello Blade！</h1>");
			}
		});
		
		blade.start();
	}
}
```

使用 `Blade.me()` 方法返回 Blade 的单例对象，在整个app中是唯一的。

方法 `blade.get` 注册一个 `/` 的路由，Blade 的默认端口是9000，运行 `main` 方法。

您应该在程序启动后看到一条日志信息：

```sh
2015-10-12 11:16:46,658 INFO [main] com.blade.Blade | Blade Server Listen on http://127.0.0.1:9000
```

现在，打开您的浏览器然后访问 [http://localhost:9000](http://localhost:9000)。您会发现，一切是如此的美好！
