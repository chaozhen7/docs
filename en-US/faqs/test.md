---
root: false
name: 测试
sort: 4
---

```java
/**
 * 初始化配置类
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
        // 在9000端口启动
        blade.app(App.class).start();
    }
}
```

```java
// 配置路由
blade.addRoute("/hello", NormalSample.class, "hello");
blade.addRoute("/save_user", NormalSample.class, "post:saveUser");

// 路由实现
public class NormalSample {
	
	public String hello(Request request, Response response){
		System.out.println("进入hello~");
		request.attribute("name", "rose baby");
		return "hi";
	}
	
	public void saveUser(Request request, Response response){
		System.out.println("进入saveUser~");
		// save操作
		JSONObject res = new JSONObject().put("status", 200);
		response.json(res.toString());
	}
}
```
```java
// 配置模板引擎
JetbrickRender jetbrickRender = new JetbrickRender();
jetEngine = jetbrickRender.getJetEngine();

blade.viewEngin(jetbrickRender);

// 使用ModelAndView

blade.get("/signup", (req, res) -> {
	ModelAndView modelAndView = new ModelAndView("signup");
	return modelAndView;
});
```

```java
// 保存操作
public boolean save(Integer cid, Integer tid, Integer fuid, Integer tuid) {
	
	return model.insert().param("cid", cid)
	.param("tid", tid)
	.param("fuid", fuid)
	.param("tuid", tuid)
	.param("addtime", new Date())
	.param("ntype", 0).executeAndCommit() > 0;
}
// 登录操作
public User signin(String username, String password) {
	String pwd = EncrypKit.md5(username + password);
	return model.select().eq("username", username)
	.eq("password", pwd).fetchOne();
}
// 查询条数
public Long getUserCount(String email){
	return model.count().eq("email", email).fetchCount();
}
```