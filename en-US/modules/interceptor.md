---
root: false
name: Interceptor
sort: 3
---

# Introduction

Blade interceptors provide a convenient mechanism to filter into the application of the HTTP request, for example, when a user is not logged in, our interpretation user login Session exists, if the user login Session does not exist, the interceptor will guide the user to the login page, however, if the user login Session exists, the interceptor will allow the request further move on.

Of course, in addition to authentication, the interceptor can also be used to perform a wide variety of tasks, such as in all requests to add the version number, when the request returns to join header information, etc.

# Create Interceptor

Create the interceptor approach is very simple, blade is like creating a routing create interceptors, but request type is `Before` Or `After`.Like this:

```java
blade.before("/.*", (req,res) -> {
    System.out.println("before request..");
});
```

The same also support, annotations way, way to register configuration file.