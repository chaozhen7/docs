---
root: false
name: 操作数据库
sort: 6
---

# 数据库操作

blade-sql2o是一款基于 `sql2o` 来更快速的操作数据库的框架。

**特性：**

+ 支持大部分Java数据类型
+ 轻松上手，采用简单的 CRUD 风格
+ 支持链式操作
+ 支持面向对象语法
+ 允许直接使用 SQL 查询／映射
+ 支持缓存数据

## 配置blade-sql2o

首先加入`blade-sql2o`组件

```xml
<dependency>
	<groupId>com.bladejava</groupId>
	<artifactId>blade-sql2o</artifactId>
	<version>最新版本</version>
</dependency>
```

然后配置数据库

+ 编码方式

```java
Sql2oPlugin sql2oPlugin = blade.plugin(Sql2oPlugin.class);
sql2oPlugin.config(url, user, pass).run();
```

+ 配置文件方式

```java
Sql2oPlugin sql2oPlugin = blade.plugin(Sql2oPlugin.class);
sql2oPlugin.load("jdbc.properties").run();
```

jdbc.properties：

```sh
blade.dburl=jdbc:mysql://127.0.0.1:3306/test
blade.dbdriver=com.mysql.jdbc.Driver
blade.dbuser=root
blade.dbpass=root
```

这样数据库配置就完成了，接下来带你写一个`Model`

```java
@Table(value="t_user", PK = "uid")
public class User implements Serializable{

	private static final long serialVersionUID = -7718091985540738974L;
	
	private Integer uid;
	
	private String loginname;

	private String password;
	
	private String mail;
	
	public User() {
	}

	public Integer getUid() {
		return uid;
	}

	public void setUid(Integer uid) {
		this.uid = uid;
	}

	public String getLoginname() {
		return loginname;
	}

	public void setLoginname(String loginname) {
		this.loginname = loginname;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getMail() {
		return mail;
	}

	public void setMail(String mail) {
		this.mail = mail;
	}
}
```

这里我们定义了一个`User`类，实现`Serializable`接口。

那么这个注解是什么意思呢？

`@Table`用于配置这个model对应数据库的表和主键是什么，如果不填写`PK`则默认为`id`

这样就配置好一个基础的model了，下面看看如何使用吧！

## CRUD使用

一般在java中我们会分为Controller、Service、Dao层，当然并不是所有人都这么划分，
在blade中依然保持Service层的存在，让Service单独去处理业务逻辑，我不怎么看好很多人把业务写在Model中。
下面的示例展示了`blade-sql2o`最基础的的CRUD

```java
@Component
public class UserServiceImpl implements UserService {
	
    private Model<User > model = Model.create(User.class);

    //根据主键查询User对象，对 就这么一行代码！
    public User getUserByUid(Integer uid){
        return model.fetchByPk(uid);
    }

    //根据登录名进行模糊查询
    public List<User> getUser(String login_name){
        return model.select().like("login_name", "%" + login_name).fetchList();
    }

    //根据uid修改email
    public boolean update(Integer uid, String email){
        return model.update().param("email", email).where("uid", uid).executeAndCommit() > 0;
    }

    //根据uid删除user
    public boolean delete(Integer uid){
        return model.delete().where("uid", uid).executeAndCommit() > 0;
    }

    //插入一条User
    public boolean save(String login_name, String email) {
		return model.insert()
		.param("login_name", login_name)
		.param("email", email).executeAndCommit() > 0;	
    }

}
```

使用起来真的so easy，大部分操作只要一行代码！！

## 高级查询

**分页查询**

```java
Page<Post> page = model.select()
        .like("title", "%" + title + "%")
        .where("`type`", type)
        .where("`status`", status)
        .orderBy(orderBy)
        .fetchPage(pageIndex, pageSize);
```

**查询记录数**

```java
public Long getUserCount(String email){
    return model.count().where("email", email).fetchCount();
}
```

**关联查询**

```java
List<User> userList = model.select("select t1.* from t_user t1 inner join t_relation t2 on t1.uid=t2.uid")
		.where("t2.friend_uid", uid).where("t2.status", 0).fetchList();
```

## 事务处理

有些时候我们会用到事务处理，比如要删除2条数据，必须通知删除成功。
下面是一个例子：

```java
@Override
public boolean delete(String pids) {
    Connection connection = model.getSql2o().beginTransaction();
    try {
        // 删除一个post
        connection = model.update().param("status", "delete").where("pid", pid).execute(connection);
        // 删除关联
        connection = model.delete("delete from t_relation").where("pid", pid).execute(connection);
        connection.commit();
        return true;
    } catch (Exception e) {
        e.printStackTrace();
        if(null != connection){
            connection.rollback();
        }
    }
    return false;	
}
```