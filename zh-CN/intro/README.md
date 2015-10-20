---
root: true
name: 框架简介
sort: 0
---

# Blade

Blade 一款简洁优雅的 Java Web 框架，微内核和模块化设计的 Web 框架。

## “微” 是什么意思？

“微”(micro) 并不表示你需要把整个 Web 应用塞进单个 Java 文件，也不意味着 Blade 在功能上有所欠缺。微框架中的“微”意味着 Blade 旨在保持核心简单而易于扩展。Blade 不会替你做出太多决策——比如使用结合 `权限管理`。而那些 Blade 所选择的——比如使用何种模板引擎——则很容易替换。除此之外的一切都由可由你掌握。如此，Blade 可以与您珠联璧合。

默认情况下，Blade 不包含数据库抽象层、表单验证，或是其它任何已有多种库可以胜任的功能。然而，Blade 支持用扩展来给应用添加这些功能，如同是 Blade 本身实现的一样。众多的扩展提供了数据库集成、表单验证、上传处理、各种各样的开放认证技术等功能。Blade 也许是“微小”的，但它已准备好在需求繁杂的生产环境中投入使用。

## 配置与惯例

Blade 繁多的配置选项在初始状况下都有一个明智的默认值，并会遵循一些惯例。 例如，按照惯例，模板文件存储在应用的 `WEB-INF` 目录里。虽然这个配置可以修改，但你通常不必这么做， 尤其是在刚开始的时候。

[API 指南](https://gowalker.org/github.com/Unknwon/macaron)

JDK 的最低版本要求为 **1.6**。

## 主要特性

- 轻量级。不依赖更多的库，摆脱SSH的臃肿，模块化设计，使用起来更轻便！
- 模块化(你可以选择使用哪些组件)
- 插件扩展机制
- Restful风格的路由接口
- 多种配置文件支持(当前支持properties、json和硬编码)
- 内置Jetty服务,模板引擎支持
- 支持JDK1.6或者更高版本

## 使用案例

- [Hello](https://github.com/bladejava/hello)：Blade的一个入门项目
- [blade-bbs](https://github.com/bladejava/blade-bbs)：简洁的论坛系统
- [blade-blog](https://github.com/bladejava/blade-blog): 单用户博客系统[进行中]
- [blade-shorturl](http://gopm.io)：短地址服务接口
- [izoe](https://github.com/biezhi/izoe)：图片、音乐社交系统[进行中]
- [更多例子](https://github.com/bladejava)

## 快速导航

- 刚开始了解 Blade 的话，不妨从 [开始使用](/docs/intro/getting_start) 看起。
- Blade 已经拥有许多 [扩展库](/docs/modules) 来简化您的工作。
- 如果您有任何问题，建议先从 [常见问题](/docs/faqs) 中寻找答案。
- 如果您觉得文档有描述得不够清楚之处，请通过 [提交工单](https://github.com/biezhi/blade/docs/issues) 告知我们。
