---
root: false
name: Get Started
sort: 1
---

# Start Using Blade

Before we start, must be clear is that the document will not teach you anything about the basic knowledge of Java language.All use of Blade's explanation is based on your existing knowledge basis.

## Install Blade

Dependence is added to the project is very simple, just make sure your version is the latest.

mvnrepository is [last_version](https://github.com/biezhi/blade/blob/master/LAST_VERSION.md) 

Here is an example:

```xml
<dependency>
    <groupId>com.bladejava</groupId>
    <artifactId>blade-core</artifactId>
    <version>x.x.x</version>
</dependency>
```

Here add the blade core library, according to your demand can add other dependencies.
Such as the template engine dependence, dependence on database operations, etc.

## Not use maven

Some students may not use maven build projects, Suggestions have the time to learn.

Do not use the maven build based on the project of blade is also very simple, as long as the jars you need to go.

`blade` core jar: `blade-kit.jar` 和 `blade-core.jar`

[Download](https://github.com/biezhi/blade/releases/) it here.

Upgrade simply replace the corresponding jar package.

## The simplest example

Create a Maven project (not the web project), in `pom.xml` Blade is added:

```xml
<dependencies>
	<dependency>
		<groupId>com.bladejava</groupId>
		<artifactId>blade-core</artifactId>
		<version>[LAST_VERSION]</version>
	</dependency>
	<dependency>
		<groupId>com.bladejava</groupId>
		<artifactId>blade-startup</artifactId>
		<version>1.0.1</version>
	</dependency>
</dependencies>
```

Create a service class, ordinary Java classes:

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

Use `Blade.me()` method returns the singleton of Blade, is unique in the entire app.


The method `blade.get` register `/` route, The Blade default port is 9000, running your main method.

You should see after the program start a log message:

```sh
2015-10-12 11:16:46,658 INFO [main] com.blade.Blade | Blade Server Listen on http://127.0.0.1:9000
```

Now, open your browser and visit [http://localhost:9000](http://localhost:9000). You will find that, everything is so beautiful!
