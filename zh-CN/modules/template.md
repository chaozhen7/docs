---
root: false
name: 模板引擎
sort: 4
---

# 模板引擎

框架默认采用的是JSP引擎进行处理，按照惯例， Blade 轻松整合了 Java 常用的几个模板引擎到 web 应用。

- `blade-jetbrick`：基于jetbrick-template模板引擎的扩展，也是本人最喜爱的模板引擎
- `blade-velocity`：基于velocity模板引擎的扩展
- `blade-beetl`：基于beetl模板引擎的扩展

## JSP渲染

默认使用了JSP引擎进行渲染，那么在开发 web 应用的时候应该注意什么呢？

- 默认的jsp视图目录是 `WEB-INF`
- Servlet3中使用EL表达式需要设置 `isELIgnored="false"`

## 模块渲染

**模板目录**

默认所有视图文件存放在`webapp`下的`WEB-INF`文件夹下，可进行设置：

```java
// 设置模板文件目录
blade.viewPrefix("/WEB-INF/views/");
```

**配置模板引擎**

```java
// 设置模板引擎
JetbrickRender jetbrickRender = new JetbrickRender();
jetEngine = jetbrickRender.getJetEngine();
blade.viewEngin(jetbrickRender);
```

**模板数据**

`blade`支持2种方式进行数据存储

+ 传统方式

```java
public String index(Request request){
    request.attribute("name", "jack");
    return "index";
}
```
+ ModelAndView

```java
public ModelAndView index(Request request){
    ModelAndView modelAndView = new ModelAndView("index");
    modelAndView.add("name", "jack");
    return modelAndView;
}
```