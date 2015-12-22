---
root: false
name: Database operation
sort: 6
---

# Database Operations

Blade - sql2o is based on a `sql2o` to faster operation of database framework.

**Features**

+ Support most Java data types
+ Easy to fit, use the simple of CRUD
+ Support chain operation
+ Support object-oriented syntax
+ Allow direct use of SQL query/map
+ Support the cached data

## Configuration

First join `blade-sql2o` dependency

```xml
<dependency>
	<groupId>com.bladejava</groupId>
	<artifactId>blade-sql2o</artifactId>
	<version>LAST_VERSION</version>
</dependency>
```

Then the configuration database

+ Hard coding way

```java
Sql2oPlugin sql2oPlugin = blade.plugin(Sql2oPlugin.class);
sql2oPlugin.config(url, user, pass).run();
```

+ The configuration file way

```java
Sql2oPlugin sql2oPlugin = blade.plugin(Sql2oPlugin.class);
sql2oPlugin.load("jdbc.properties").run();
```

jdbc.properties:

```sh
blade.dburl=jdbc:mysql://127.0.0.1:3306/test
blade.dbdriver=com.mysql.jdbc.Driver
blade.dbuser=root
blade.dbpass=root
```

This database configuration is done, then take you to write a `Model`

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

Here we define a `User` class, realize `Serializable` interface.

This annotation is what mean ?

`@Table` this model is used to configure the corresponding database table and what is the primary key, if not complete `PK` default `id`.

Thus configured a basic model, the following look at how to use!

## CRUD

General in Java we can into the Controller, the Service, Dao layer, of course, not everyone is so divided,
Still in the blade of the existence of the Service layer, let alone Service to handle the business logic, I don't really like a lot of people write operations in the Model.

The following example shows a `blade-sql2o` most basic CRUD:

```java
@Component
public class UserServiceImpl implements UserService {

    private Model<User > model = new Model<User>(User.class);

    // According to the primary key queries the User object, just this one line of code！
    public User getUserByUid(Integer uid){
        return model.fetchByPk(uid);
    }

    // According to the fuzzy query login name
    public List<User> getUser(String login_name){
        return model.select().like("login_name", "%" + login_name).fetchList();
    }

    // According to the uid changing the E-mail
    public boolean update(Integer uid, String email){
        return model.update().param("email", email).where("uid", uid).executeAndCommit() > 0;
    }

    // According to the uid to delete the user
    public boolean delete(Integer uid){
        return model.delete().where("uid", uid).executeAndCommit() > 0;
    }

    // Insert a User
    public boolean save(String login_name, String email) {
		return model.insert()
		.param("login_name", login_name)
		.param("email", email).executeAndCommit() > 0;	
    }

}
```

Really so easy to use and most operating as long as one line of code !!

## Advanced query

**Page query**

```java
Page<Post> page = model.select()
        .like("title", "%" + title + "%")
        .where("`type`", type)
        .where("`status`", status)
        .orderBy(orderBy)
        .fetchPage(pageIndex, pageSize);
```

**Query record number**

```java
public Long getUserCount(String email){
    return model.count().where("email", email).fetchCount();
}
```

**Join Query**

```java
List<User> userList = model.select("select t1.* from t_user t1 inner join t_relation t2 on t1.uid=t2.uid")
		.where("t2.friend_uid", uid).where("t2.status", 0).fetchList();
```

## Transaction

There are times when we would use the transaction processing, such as to delete 2 data, must inform deleted successfully.

Here is an example:

```java
@Override
public boolean delete(String pids) {
    Connection connection = model.getSql2o().beginTransaction();
    try {
        // delete a post
        connection = model.update().param("status", "delete").where("pid", pid).execute(connection);
        // delete relate
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