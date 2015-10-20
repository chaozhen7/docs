---
root: false
name: Active Record 基础
sort: 3
---

# Active Record 基础

## 1. Active Record 是什么？

Active Record 是 MVC 中的 M（模型），处理数据和业务逻辑。Active Record 负责创建和使用需要持久存入数据库中的数据。Active Record 实现了 Active Record 模式，是一种对象关系映射系统。

### 1.1 Active Record 模式

Active Record 模式出自 [Martin Fowler](http://www.martinfowler.com/eaaCatalog/activeRecord.html) 的《企业应用架构模式》一书。在 Active Record 模式中，对象中既有持久存储的数据，也有针对数据的操作。Active Record 模式把数据存取逻辑作为对象的一部分，处理对象的用户知道如何把数据写入数据库，以及从数据库中读出数据。

### 1.2 对象关系映射

对象关系映射（ORM）是一种技术手段，把程序中的对象和关系型数据库中的数据表连接起来。使用 ORM，程序中对象的属性和对象之间的关系可以通过一种简单的方法从数据库获取，无需直接编写 SQL 语句，也不过度依赖特定的数据库种类。

### 1.3 Active Record 用作 ORM 框架

Active Record 提供了很多功能，其中最重要的几个如下：

- 表示模型和其中的数据；
- 表示模型之间的关系；
- 通过相关联的模型表示继承关系；
- 持久存入数据库之前，验证模型；
- 以面向对象的方式处理数据库操作；

## 2. Active Record 中的“多约定少配置”原则

使用其他编程语言或框架开发程序时，可能必须要编写很多配置代码。大多数的 ORM 框架都是这样。但是，如果遵循 Rails 的约定，创建 Active Record 模型时不用做多少配置（有时甚至完全不用配置）。Rails 的理念是，如果大多数情况下都要使用相同的方式配置程序，那么就应该把这定为默认的方法。所以，只有常规的方法无法满足要求时，才要额外的配置。

### 2.1 命名约定

默认情况下，Active Record 使用一些命名约定，查找模型和数据表之间的映射关系。Rails 把模型的类名转换成复数，然后查找对应的数据表。例如，模型类名为 Book，数据表就是 books。Rails 提供的单复数变形功能很强大，常见和不常见的变形方式都能处理。如果类名由多个单词组成，应该按照 Ruby 的约定，使用驼峰式命名法，这时对应的数据表将使用下划线分隔各单词。因此：

- 数据表名：复数，下划线分隔单词（例如 book_clubs）
- 模型类名：单数，每个单词的首字母大写（例如 BookClub）

---------------------------
|模型 / 类	|数据表 / 模式|
---------------------------
|Post	|posts|
---------------------------
|LineItem	|line_items|
---------------------------
|Deer	|deers|
---------------------------
|Mouse	|mice|
---------------------------
|Person	|people|
---------------------------

### 2.2 模式约定

根据字段的作用不同，Active Record 对数据表中的字段命名也做了相应的约定：

- **外键** - 使用 singularized_table_name_id 形式命名，例如 item_id，order_id。创建模型关联后，Active Record 会查找这个字段；
- **主键**主键 - 默认情况下，Active Record 使用整数字段 id 作为表的主键。使用 Active Record 迁移创建数据表时，会自动创建这个字段；

还有一些可选的字段，能为 Active Record 实例添加更多的功能：

- created_at - 创建记录时，自动设为当前的时间戳；
- updated_at - 更新记录时，自动设为当前的时间戳；
- lock_version - 在模型中添加乐观锁定功能；
- type - 让模型使用单表继承；
- (association_name)_type - 多态关联的类型；
- (table_name)_count - 缓存关联对象的数量。例如，posts 表中的 comments_count 字段，缓存每篇文章的评论数；

> 虽然这些字段是可选的，但在 Active Record 中是被保留的。如果想使用相应的功能，就不要把这些保留字段用作其他用途。例如，type 这个保留字段是用来指定数据表使用“单表继承”（STI）的，如果不用 STI，请使用其他的名字，例如“context”，这也能表明该字段的作用。

## 3. 创建 Active Record 模型

创建 Active Record 模型的过程很简单，只要实现 `Serializable` 接口就行了：

```java
@Table(value = "bbs_option", PK = "opt_key")
public class Option implements Serializable {

	private static final long serialVersionUID = -7718091985540738974L;

	private String opt_key;
	private String opt_value;

	public Option() {
		// TODO Auto-generated constructor stub
	}

	public String getOpt_key() {
		return opt_key;
	}

	public void setOpt_key(String opt_key) {
		this.opt_key = opt_key;
	}

	public String getOpt_value() {
		return opt_value;
	}

	public void setOpt_value(String opt_value) {
		this.opt_value = opt_value;
	}

}
```

上面的代码会创建 `Option` 模型，对应于数据库中的 `bbs_option` 表。同时，`bbs_option` 表中的字段也映射到 `Option` 模型实例的属性上。

## 4. CRUD：读写数据

CURD 是四种数据操作的简称：C 表示创建，R 表示读取，U 表示更新，D 表示删除。Active Record 自动创建了处理数据表中数据的方法。

### 4.1 创建

Active Record 对象可以使用 Hash 创建，在块中创建，或者创建后手动设置属性。new 方法创建一个新对象，create 方法创建新对象，并将其存入数据库。
