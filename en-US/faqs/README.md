---
root: true
name: FAQ
---

# FAQ

### jdk7下出现`java.lang.UnsupportedClassVersionError: org/eclipse/jetty/server/Handler`

在jdk7下运行`hello`应用时会出现：

```java
2015-11-08 18:20:49,241 DEBUG [main] com.blade.route.Router | Add Route：GET	/
Exception in thread "main" java.lang.UnsupportedClassVersionError: org/eclipse/jetty/server/Handler : Unsupported major.minor version 52.0
	at java.lang.ClassLoader.defineClass1(Native Method)
	at java.lang.ClassLoader.defineClass(ClassLoader.java:800)
```

这个错误，其原因是jetty实现对应的jdk版本不同，`blade-startup`默认引入的 `jetty-9.3.4` 这个版本，对jdk7不兼容。

解决方法，只需要注释掉`blade-startup`的依赖，加入以下配置即可：

```xml
<dependency>
		    <groupId>org.eclipse.jetty</groupId>
		    <artifactId>jetty-server</artifactId>
		    <version>9.2.12.v20150709</version>
		</dependency>
        
        <dependency>
		    <groupId>org.eclipse.jetty</groupId>
		    <artifactId>jetty-webapp</artifactId>
		    <version>9.2.12.v20150709</version>
		</dependency>
```

在`blade-startup`的下一个版本中会修复该问题！

**jetty各个版本信息**

![](http://i.imgur.com/cc35Yjd.png)

