---
root: false
name: Configuration
sort: 1
---

# Introduction

The operation of the Blade framework is depends on the configuration, each option has a specification, so you can easily browse these documents, and are familiar with these options to configure.

# Configuration

Blade currently supported JSON format, and the Properties of the configuration file parsing, also can be hard-coded configuration, but the default used the analytic Properties format, the user can through simple configuration you can gain a lot of flexibility.

# Configuration mode

We create a blade based application, initial configuration must create a class inherits from `Bootstrap` class:

```java
public class App extends Bootstrap {

	@Override
	public void init(Blade blade) {}

	@Override
	public void contextInitialized(Blade blade) {}
}
``` 

Bootstrap is an abstract class, for the use of global initialization.There are two methods:

- `init`: initialization operation
- `contextInitialized`: system initialization after operation, the source, you will find here can get more things (but) are not frequently used


> _**General in the launch configuration class init method of routing configuration, the template configuration, database configuration**_


# Default Config

Blade built in some basic configuration for developers use

```sh
# view file directory
blade.prefix=/WEB-INF/
# view suffix
blade.suffix=.jsp
```

If you will use jetty and web application starts, the blade enabled by default port is 9000.

# Read Config Value

```java
Blade blade = Blade.me();
String value = blade.config().get( KEY );
```

Through the above ways to get configuration file parameters configuration.

`Config` Supports the following methods:

```java
get(String key)
getAsInt(String key)
getAsLong(String key)
getAsBoolean(String key)
getAsDouble(String key)
getAsFloat(String key)
```

## Configuration Collection

- `blade.ioc`: Configuration to manage the package name of the ioc, multiple use of commas.
- `blade.prefix`: View directory prefix, such as `/WEB-INF/views/` to `/` the beginning and end.
- `blade.suffix`: View file suffix, is the default is `.jsp`, you can modify for other.
- `blade.filter_folder`: Static file directory, the request these directories will be filtered out
- `blade.route`: Configuration annotation routing package name, multiple use commas
- `blade.interceptor`: Configuration of the interceptor package name, multiple use commas (don't recommend this way)
- `blade.view404`: Configuration 404 template page
- `blade.view500`: Configuration 500 template page
- `blade.enableXSS`: Configure whether to enable XSS defenses, is not enabled by default

Actually very simple configuration, what what you need to join, modular development really so easy ~

Next, to explore the secret of the [Route](./route) !
