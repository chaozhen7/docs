---
root: false
name: MVC Architecture
sort: 3
---

# MVC Architecture

## Concept

Blade from Rails and Play!Absorb a lot of mature design ideas, many of the same idea is used in the framework of design and interface.

Blade through simple agreed to support the MVC design pattern, light weight, high development efficiency.

## MVC

- Model describes the basic data objects, a particular query and update the logic.
- View some templates for the data presented to the user.
- Controller to perform the user's request to the data according to user's requirements, and specify the template rendering.

Summary of some good MVC structure, like [Play!](http://www.playframework.org/) and Blade match exactly.

## Overall Design

![](https://i.imgur.com/fNxaeoi.png)

`blade` is based on the `blade-core` for the construction of the core, is a highly decoupled framework.
`blade` consideration at the beginning of the modular design, the plugin to use, with independent components development, part of the component does not rely on `blade`, for example, you can use a `blade-cache` module to do your cache logic; use `blade-kit` as the basis of your kit.

## Perform Logical

Since ` blade ` is built based on the core module, so his execution logic is what?` blade ` is a typical MVC architecture, his execution logic as shown in the figure below:

 ![](https://i.imgur.com/joP7aBH.png)

## Project Structure

General `blade` project directory as shown below, is a type of maven project:

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

We can see from the above directory structure M（models）、V（views）和 C（controllers）structure, `App.java` is the entry file。

# Contribute code

`blade` is free and open source software, which means that anyone can make contributions to the development and progress.

`blade` source code is hosted on a lot, lot provides a very easy way to fork the project and merge your contribution.

**Pull Requests**

A pull request process for new features and bug are not the same.When you launch a new feature of the pull request before, you should first create a `[Proposal]` title issue.The proposal should describe this new feature, as well as the realization method.Proposal will be review, could be adopted, are also likely to be rejected.When a proposal is adopted, will create an implementation of the new features of the pull request.Not follow the guidelines of the pull request will be shut down immediately.
For bug create Pull requests do not need to create suggested that issue.If you have a solution to the bug, please describe in detail your solution.

# Submit new features

If you want blade in a new feature, you can create a with in making ` request ` title issue.The proposal will be the core code contributors review.