---
root: false
name: Route
sort: 2
---

# Route Module

Modern Web application URL is very elegant, easy to people recognition memory。
In the Blade, the routing is an HTTP method matching a URL matching model, each routing method can correspond to one or more processors, as shown below:

## Basic Route

```java
blade.get("/").run(req, res) -> {
    // show something
});

blade.post("/").run(req, res) -> {
    // create something
});

blade.put("/").run(req, res) -> {
    // replace something
});

blade.delete("/").run(req, res) -> {
    // destroy something
});
```

## For a variety of routing request registration

Configure a routing file here（Matching is `com.example.controller` class under the package）

```java
blade.routeConf("com.example.controller", "route.conf");
```

`route.conf` writing:

```
GET     /               IndexRoute#home
POST    /signin         IndexRoute#signin
GET     /users          UserRoute#show_users
GET     /users/:uid     UserRoute#show_user
```

## All registered routing response HTTP requests

```java
blade.any("/").run(req, res) -> {
    // ...
});
```


Some notes:

- Routing order of matching is executed in the order they are defined
- However, match the smaller routing priority than matching large range of high priority.
- First defined the routing will be the first to be matching user requests and calls

### Placeholder

Use a specific name to represent some part of the route:

```java
blade.get("/hello/:name").run(req, res) -> {
    String name = req.pathParam("name");
});

blade.get("/date/:year/:month/:day").run(req, res) -> {
    System.out.println("Date:  %s/%s/%s", req.param("year"), req.param("month"), req.param("day"))
});
```

### Functional Route

Route can also use a common to register the classes and methods:

```java
public class App extends Bootstrap {

    @Override
    public void init() {
    	Blade blade = Blade.me();
    	blade.addRoute("/", Index.class, "index");
    	blade.addRoute("/hello", Index.class, "hello");
    	blade.addRoute("/save", Index.class, "save", HttpMethod.POST);
    }
}
```

**`Index.java`**

```java
public class Index{

	public String index(){
		return "index";
	}

	public void hello(Response res){
		System.out.println("hello");
		res.html("<h1>hello~</h1>");
	}

	public void save(Response res){
		// save opt...
		res.json("{'status':200}");
	}
}
```

### Annotations Route

This kind of writing is probably the most used to it, everyone is one of the common writing, use annotations to configure a route

```java
@Path("/")
public class Hello {
    
    @Route("hello")
    public String hello() {
        System.out.println("hello");
        return "hello.jsp";
    }
        
    @Route(value = "post", method = HttpMethod.POST)
    public void post(Request request) {
        String name = request.query("name");
        System.out.println("name = " + name);
    }
    
    @Route("users/:name")
    public ModelAndView users(Request request, Response response) {
        System.out.println("users");
        String name = request.pathParam(":name");
        
        ModelAndView modelAndView = new ModelAndView("users");
        modelAndView.add("name", name);
        return modelAndView;
    }

    @Route("index")
    public String index(Request request) {
        request.attribute("name", "jack");
        return "index.jsp";
    }
}
```

**What is the @Path?**

+ Each routing class must with the `@Path` identification, this routing parser know this is a routing class
+ `@Path` annotations only a `value` parameter configuration, equivalent to a namespace
    
**What is the @Route?**

+ `Route` is the minimum unit, a routing method is used to
+ The `Route` has the value of parameters, method
+ `value` URL for routing configuration, also is the path of the HTTP request
+ Does not begin with `/` commonly, because `@Path` will specify, written as follows:
    * /hello
    * /user/hello
    * /user/:uid
    * /user/:uid/post
    
+ The routing configuration Rest style, must be suitable for your taste！

**The basis of the support of all functions as shown below**

```java
get(path, CallBack);
post(path, CallBack);
delete(path, CallBack);
put(path, CallBack);
route(path, CallBack);
any(path, CallBack);
```

So, configured routing are there any questions?

- How to receive the request data ?
- How to deal with the upload ?
- How return json ?
- The interceptor how to play ?

The following chapter take you understand these features, [Come with me](./requestresponse)
