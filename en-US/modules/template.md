---
root: false
name: View And Template
sort: 5
---

# Template Engin

Framework by default USES the JSP engine for processing, by convention, Blade easily commonly used several templating engine to integrate the Java web applications.

- `blade-jetbrick`: Based on jetbrick - the expansion of the template template engine, is also my favorite template engine
- `blade-velocity`: Based on the expansion of the velocity template engine
- `blade-beetl`: Based on the extension beetl template engine

## JSP Render

Default to the JSP engine to render, then in the development of web application, what should you pay attention to ?

- The default is `WEB-INF` JSP view directory
- You need to set up Servlet3 the use of EL expression `isELIgnored="false"`

**Template directory**

Under all the default view files stored in webapp `WEB-INF` folder, can be setting:

```java
// Setting a template file directory
blade.viewPrefix("/WEB-INF/views/");
```

**How to output to the page ?**

```java
public void index(Request request, Response response){
    request.attribute("name", "jack");
    response.render("index");
}
```

Blade framework built into the JSP rendering, so you can see `response.render("index")` code can be returned to the `WEB-INF/index.jsp` file,
Where is this file, please look at the front to the **Template directory**.

Blade also supports similar spring ModelAndView, of course, how to use ?

```java
public void index(Request request, Response response){
    ModelAndView modelAndView = new ModelAndView("index");
    modelAndView.add("name", "jack");
    response.render(modelAndView);
}
```

Here, is very simple.What do you mean ?

ModelAndView object is a view model, in the spring, there is the store view and data,
You can look at its source, the data is stored in the `Map`, `String` variable for the view file.

## Configuration template engine

```java
// Setting Templte Engine
JetbrickRender jetbrickRender = new JetbrickRender();
jetEngine = jetbrickRender.getJetEngine();
blade.viewEngin(jetbrickRender);
```

The use of other more operation will follow the template method.
