---
root: false
name: 操作数据库
sort: 6
---

# 数据库操作

blade-jdbc是一款基于 [sql2o](http://www.sql2o.org) 来更快速的操作数据库的框架。

**特性：**

+ 支持大部分Java数据类型
+ 轻松上手，采用简单的 CRUD 风格
+ 支持链式操作
+ 支持面向对象语法
+ 允许直接使用 SQL 查询／映射
+ 支持缓存数据

## 配置blade-jdbc

首先加入`blade-jdbc`组件

```xml
<dependency>
	<groupId>com.bladejava</groupId>
	<artifactId>blade-jdbc</artifactId>
	<version>最新版本</version>
</dependency>
```

然后配置数据库

```java
// 连接数据库
DB.open("com.mysql.jdbc.Driver", "jdbc:mysql://127.0.0.1/test", "root", "root");
```

+ 配置文件方式

```java
// 配置数据库
DB.open(blade.config().get("jdbc.driver"),
		blade.config().get("jdbc.url"),
		blade.config().get("jdbc.user"),
		blade.config().get("jdbc.pass"), true);
```

blade.properties：

```sh
# db
jdbc.driver = com.mysql.jdbc.Driver
jdbc.url = jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&amp;characterEncoding=UTF-8
jdbc.user = root
jdbc.pass = root
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
下面的示例展示了`blade-jdbc`最基础的的CRUD

```java
@Service
public class UserServiceImpl implements UserService {

	@Override
	public User getUser(Long uid) {
		return AR.findById(User.class, uid);
	}

	@Override
	public User getUser(QueryParam queryParam) {
		return AR.find(queryParam).first(User.class);
	}

	@Override
	public List<User> getUserList(QueryParam queryParam) {
		if(null != queryParam){
			return AR.find(queryParam).list(User.class);
		}
		return null;
	}

	@Override
	public boolean delete(Long uid) {
		if(null != uid){
			AR.update("delete from t_user where uid = ?", uid).executeUpdate();
			return true;
		}
		return false;
	}

	@Override
	public User signin(String loginName, String passWord) {
		String pwd = EncrypKit.md5(loginName + passWord);
	    User user = AR.find("select * from t_user where login_name = ? and pass_word = ? and status = ?", loginName, pwd, 1).first(User.class);
		return user;
	}

	@Override
	public boolean updateStatus(Long uid, Integer status) {
		if(null != uid && null != status){
			AR.update("update t_user set status = ? where uid = ?", status, uid).executeUpdate();
			return true;
		}
		return false;
	}

}
```

使用起来真的so easy，大部分操作只要一行代码！！

## 高级查询

**分页查询**

```java
QueryParam queryParam = QueryParam.me();
queryParam.page(1, 10);
AR.find(queryParam).page(User.class);
```

**查询记录数**

```java
public Long getUserCount(){
	return AR.find("select count(1) from t_user").first(Long.class);
}
```

## 事务处理

待处理，目前支持，但是不是很优雅。
