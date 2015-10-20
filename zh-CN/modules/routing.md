---
root: false
name: 路由模块
sort: 2
---

# 路由模块

现代 Web 应用的 URL 十分优雅，易于人们辨识记忆。
在 Blade 中, 路由是一个 HTTP 方法配对一个 URL 匹配模型， 每一个路由可以对应一个或多个处理器方法，如下所示:

```java
blade.get("/").run(req, res) -> {
    // show something
});

blade.patch("/").run(req, res) -> {
    // update something
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

blade.options("/").run(req, res) -> {
    // http options
});

blade.all("/").run(req, res) -> {
    // do anything
});
```

几点说明：

- 路由匹配的顺序是按照他们被定义的顺序执行的，
- 但是，匹配范围较小的路由优先级比匹配范围大的优先级高（详见 **匹配优先级**）。
- 最先被定义的路由将会首先被用户请求匹配并调用。

### 占位符

使用一个特定的名称来代表路由的某个部分：

```java
blade.get("/hello/:name").run(req, res) -> {
    String name = req.pathParam(":name");
});

blade.get("/date/:year/:month/:day").run(req, res) -> {
    System.out.println("Date:  %s/%s/%s", req.pathParam(":year"), req.pathParam(":month"), req.pathParam(":day"))
});
```

当然，想要偷懒的时候可以将 `:` 前缀去掉：

```java
blade.get("/hello/:name").run(req, res) -> {
    String name = req.pathParam("name");
});

blade.get("/date/:year/:month/:day").run(req, res) -> {
    System.out.println("Date:  %s/%s/%s", req.pathParam("year"), req.pathParam("month"), req.pathParam("day"))
});
```

### 函数式路由

路由还可以使用一个普通的类和方法来注册：

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

### 注解路由

这种写法可能是大家最习惯的，也是常见的写法之一，用注解配置一个路由

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

**@Path是什么？**

+ 每一个路由的类必须用`@Path`标识，这样路由解析器知道这是一个路由类
+ `@Path`注解只有一个value参数配置，相当于`namespace`
    
**@Route是什么？**

+ `Route`是一个路由的最小单元，用于方法上
+ `Route`的参数有`value`，`method`，`acceptType`
+ `value`用于配置路由的URL，也就是http请求的路径
+ 一般不以`/`开头，因为`@Path`上会指定的，写法如下：
    * /hello
    * /user/hello
    * /user/:uid
    * /user/:uid/post
    
+ Rest风格的路由配置，一定适合你的口味！

**所有的支持的基础函数如下所示**

```java
get(path, CallBack);
post(path, CallBack);
delete(path, CallBack);
put(path, CallBack);
patch(path, CallBack);
head(path, CallBack);
trace(path, CallBack);
options(path, CallBack);
connect(path, CallBack);
all(path, CallBack);
```

那么，配置好了路由大家还有什么疑问呢？

- 请求的数据如何接收？
- 怎么处理上传？
- json怎么返回？
- 拦截器该怎么玩？

下面的一个章节带你了解这些功能，[一起来吧](./request_process)
