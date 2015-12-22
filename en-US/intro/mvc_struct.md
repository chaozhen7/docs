---
root: false
name: MVC Architecture
sort: 3
---

# MVC架构

## 概念

Blade 从Rails 和 Play! 中吸收了许多成熟的设计思想, 许多相同的思想被用到了框架的设计和接口中。

Blade 通过简单的约定来支持 MVC 设计模式，轻量、开发效率高。

## MVC

- 模型 描述基本的数据对象，特定的查询和更新逻辑。
- 视图 一些模板，用于将数据呈现给用户。
- 控制器 执行用户的请求，准备用户所需的数据，并指定模板进行渲染。

一些不错的MVC结构概述，像 [Play!](http://www.playframework.org/) 框架 与Blade框架完全匹配。

## 整体设计

![](https://i.imgur.com/fNxaeoi.png)

`blade`是基于`blade-core`为核心的构建的，是一个高度解耦的框架。
`blade`设计之初就考虑了模块化、插件化去使用，用独立的组件进行开发，部分组件不依赖`blade`，例如：你可以使用`blade-cache`模块来做你的缓存逻辑；使用`blade-kit`模块来作为你的基础工具包。

## 执行逻辑

既然`blade`是基于核心模块构建的，那么他的执行逻辑是怎么样的呢？`blade`是一个典型的MVC架构，他的执行逻辑如下图所示：

 ![](https://i.imgur.com/joP7aBH.png)

## 项目结构

一般的`blade`项目的目录如下所示，是一个maven类型的项目：

```
├── java
│   ├── App.java
│   ├── controller
│   ├── service
│   ├── model
│   └── interceptor
├── resources
│   └── blade.properties
└── webapp
    ├── static
    └── WEB-INF
        ├── web.xml
        └── views
```
从上面的目录结构我们可以看出来 M（models 目录）、V（views 目录）和 C（controllers 目录）的结构， `App.java` 是入口文件。

# 贡献代码

`blade` 是免费、开源的软件，这意味着任何人都可以为其开发和进步贡献力量。

`blade` 源代码目前托管在Github上，Github提供非常容易的途径fork项目和合并你的贡献。

**Pull Requests**

pull request 的处理过程对于新特性和bug是不一样的。在你发起一个新特性的pull request之前，你应该先创建一个带有`[Proposal]`标题的issue。这个proposal应当描述这个新特性，以及实现方法。提议将会被审查，有可能会被采纳，也有可能会被拒绝。当一个提议被采纳，将会创建一个实现新特性的pull request。没有遵循上述指南的pull request将会被立即关闭。
为bug创建的Pull requests不需要创建建议issue。如果你有解决bug的办法，请详细描述你的解决方案。

# 提交新特性

如果你希望blade中出现某个新特性，你可以在Github中创建一个带有 `request` 标题的issue。该建议将会被核心代码贡献者审查。
