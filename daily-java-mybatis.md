

### **01.07-01.08复习mysql**

root：临时密码password

```sql
mysql -uroot -p123456  --连接数据库
update mysql.user set authentication_string=password('123456')where user='root' and Host = 'localhost'; --修改用户密码 
flush privileges;   --刷新权限
--------------
--两个单横线是注释
--注意语句以分号;结尾
show databases; --查看所有的数据库

mysql>use school --切换数据库， 使用：use 数据库名
Database changed 

show tables;  --查看数据库中的表
describe student;  --显示数据库中student表的信息

creat database westos; --创建一个数据库westos
exit;  --退出连接

```



操作数据库:

```sql
CREATE DATABASE IF NOT EXISTS www  --创建数据库，加if not exists防止重复
--数据库中自动变成大写
DROP DATABASE IF EXISTS www	--删除数据库www
USE `user`   
SELECT `USER` FROM student  --查询student表中USER字段的信息
SHOW DATABASE

```

数据库列类型：

```sql
data  YYYY-MM-DD,日期格式
time  HH: mm: ss 时间格式
datetime YYYY-MM-DD HH:mm :ss最常用的时间格式
timestamp  时间戳，19701.1到现在的毫秒数
---
```

数据库字段属性：

```sql
Unsigned:是无符号整数，该列不能为负数
zerofill:不足位数0填充，比如int（3） 输入5，结果为005
自增：通常是用于设计整数类型的主键，自增；
非空 NULL not null ，这个时候不赋值会报错
NULL，如果不填，就是null

```

创建数据库表：

```sql
CREATE TABLE IF NOT EXISTS `student`(
--表的名称(student)和字段尽量用` `括起来，防止因为是关键字而造成报错
--字符串一般使用单引号
--AUTO_INCREMENT是自增
	`id`  INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT  '匿名' COMMENT '姓名',
	--default是设置默认值
	`pwd` VARCHAR(20)NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2)NOT NULL DEFAULT '男' COMMENT '性别',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '电子邮箱',
	--添加主键PRIMARY KEY
	PRIMARY KEY(`id`)
	
)ENGINE INNODB DEFAULT CHARSET = utf8
```

修改与删除表/表中字段：

```sql
ALTER TABLE teacher RENAME AS teacher1
--修改表名
ALTER TABLE teacher1 ADD age INT(11)
--增加表的字段age
ALTER TABLE teacher1 MODIFY age VARCHAR(11)
ALTER TABLE teacher1 CHANGE age age1 INT(1)
--change和modify区别：modify用于修改约束，change用来给字段重命名

ALTER TABLE teacher1 DROP age1 --删除表的字段
DROP TABLE IF EXISTS teacher1  --删除表
--sql关键字大小写不敏感
```



外键：

```sql
---方式一创建表时添加
KEY `FK_gradeid`--定义键
--FK_gradeid是约束名
CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`) --给外键添加约束，CONSTRAINT为约束，REFENENCES为引用
当存被其他表引用（有外键关系）的时候此表是无法直接删除表的,需要先把那个表删除
--方式二创建后加外键
ALTER TABLE `student`
ADD CONSTRAINT `FK_gradeid` FORERIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`);
--就是说：添加 约束名 外键（）引用 那个表 表中的(哪个字段)

```

其实，数据库存储数据即可，行为数据，列为字段；

后续用程序即可实现使用多张表的数据

DML（增删改）：数据操作语言

insert update delete 



select（不是

insert，update

```sql
INSERT INTO `表名`(`字段1`,`字段2`)values('值1'),('值2')(...)
例如：
INSERT INTO `people`(`id`,`name`,`age`,`address`)VALUES('648','克里斯','18','光明大街18号')
UPDATE `表名` SET `name` = 'xxx' WHERE id = 1 --一定要加附加条件
DELETE FROM `表名` WHERE id = 1;
TRUNCATE `student` --清空student整个表
```

select (DQL，数据查询语言)

```sql
SELECT * FROM student --查询全部学生
SELECT * FROM score  
SELECT `列名1`, `列名2` FROM student
SELECT `StudentNo` AS 学号, `StudentName` AS 学生姓名 FROM student--这样用别名就很直观
--拼接字符串CONCAT(a,b)
SELECT CONCAT('姓名：',StudentName) AS 新名字 FROM student
```



### 01.09-01.11复习javaweb与MVC



1.动态web资源开发的技术统称javaweb

2.Maven 

​		可以更快自动导入并配置管理jar包

**Maven项目架构管理工具**

核心思想：**约定大于配置**，不要违反约束

Maven会规定号如何去编写我们的java代码

在apache官网中,目前是3.6.3

添加阿里云镜像；

建立本地仓库：E:\Environment\apache-maven-3.6.3\maven-repo

 IDEA项目创建成功后，需要看一眼Maven的配置



配置一下tomcat

g，a，v

war包：javaweb应用

（时间不够，跳了一部分

**javabean** （可以理解为实体类）

一般用来与数据库的字段做映射 ORM；

ORM：对象关系映射

一般是一一对应：实体类对应数据库的一张表：s

比如class People 和table  people和jsp页面

```java
public class People{
    //实体类，一般和数据库中的表结构对应
    private int id;//注意javabean里面都是私有化属性
    private String name;
    private int age;
    private String address;
    public People(){}
    
    public People(int id,String name,int age, String address){
        this.id = id;
        this.name = name;
        this.age = age;
        this.address = address; 
    }
    public int getId(){
        return id;
    }
     public int setId(){
        this.id = id;
    }
     public int getName(){
        return name;
    }
     public int setId(){
        this.name = name;
    }
    //....其他同上
    
    
}
```

数据库中对应建立一个表people



```jsp

<%

  //People people = new People();

%>或者书写如下

<jsp:usebean id="people" class ="" scope ="page">
<jsp:setProperty name="people" property = "address" value = ""/>


```





#### 复习MVC三层架构：

M：模型层，service（业务逻辑）+Dao（数据持久层，用来操作数据库的实体类）+javabean

V：视图层，jsp，展示数据，提供用户操作，提供链接发起servlet请求，

C：控制层，servlet，1，接收用户的请求（request请求参数，session信息）

​									2，交给业务层处理对应的代码

​									3，控制视图跳转（响应给客户端内容，重定向或者转发



```java
例子：
登录-->接收用户的登陆请求-->处理用户的请求（获取用户登录的参数，usernam，password）——->交给业务层登录业务（判断用户名密码是否正确：事务）-->Dao层查询用户名和密码是否正确-->数据库
```

一般servlet专注于处理请求，并控制视图跳转；

JSP用于显示数据；

![mvc](C:\Users\dell\Desktop\Typora专属图片文档\mvc.png)











### **01.11-01.15学习Mybatis3**(bat..)

环境：JDK1.8，mysql5.7/8.0，maven3.6.3 ，IDEA

注：Mybatis有中文版本，是ssm中的m

1.简介

​    优秀的持久层框架，避免了JDBC和手动设置参数以及获取结果集;原名iBatis，13年迁至GitHub。

目前最新3.5.3

最多人的：https://mvnrepository.com/artifact/org.mybatis/mybatis/3.4.6

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

**持久层**

数据持久化：

​	将程序的数据在持久状态和瞬时状态转化的过程

​	内存：断电即失

​	数据库（jdbc），io文件持久化

why：防止内存断电丢失数据

**Mybatis：**

帮助程序员将数据存入到数据库中，省略传统的复杂JDBC；

半自动化，

sql和代码分离，

提供映射，支持对象与数据库的orm字段关系映射

提供xml标签，支持编写动态sql

**第一个Mybatis程序**

思路：搭建环境->导入Mybatis->编写代码->测试

```sql
CREATE DATABASE `mybatis`;
USE `mybatis`;

CREATE TABLE `user`(
`id` INT(20) NOT NULL,
`name` VARCHAR(30) DEFAULT NULL,
`pwd` VARCHAR(30) DEFAULT NULL,
PRIMARY KEY (id)

)ENGINE=INNODB DEFAULT CHARSET =utf8;--引擎为INNODB

INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
(1,'疯帽','123456'),
(2,'大范','123456'),
(3,'黑炭','123886')--注意最后一行没有逗号


```

创建maven项目

![2](C:\Users\dell\Desktop\Typora专属图片文档\2.png)

注意maven每次都要 看看是否是自己的路径，否则会下载到c盘

删除src目录，因为这样可以当做父工程，导入依赖（mybatis，mysql，junit）

环境搞定后可以开始写代码了：

创建一个子模块：

然后新建xml文件（在src的resouces下）：

xml设置value的&符号需要换成& amp;(中间没空格)

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8amp;&useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
    </environments>
   
</configuration>
```

```java
其中：
    String url = "jdbc:mysql://localhost:3306/jdbc?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&useSSL=true";
```

编写mybatis工具类：

 子模块mybatis01下java文件夹新建包xx.xx

然后包下包含dao和utils，utils包含了这个工具类

```java
package com.wang.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
//sqlSessionFactory	=>	sqlsession
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;//提升作用域
    static {

        try {
            //使用Mybatis第一步：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //既然有了SqlSessionFactory，顾名思义我们就可以从中获取SqlSession的实例了
    //SqlSession 完全包含了面向数据库执行sql命令所需的所有方法
    public static SqlSession getSqlSession(){
        return  sqlSessionFactory.openSession();


    }

}
```

编写代码：

​	实体类

​	Dao接口

​	接口实现类



```java
//1.实体类
//其中pojo可以理解为javabean功能
package com.wang.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;
    public User(){}

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

```

接口类：Userdao

xml中对着<>中最后>前打出空格即可浏览所有可附加的方法

```java
//接口
package com.wang.dao;

import com.wang.pojo.User;

import java.util.List;

public interface UserDao {//Dao也就是mapper的含义

    List <User> getUserList();

}

```

接口实现类：由原来的UserDaoImpl转变为一个Mapper配置文件

（原来定义Userdao接口后，会定义UserdaoImpl类去实现接口的功能，现在只需要xml配置文件去设置对应方法的sql语句即可。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.wang.dao.UserDao">
<!--select查询语句-->
    <select id="getUserList" resultType="com.wang.pojo.User">
    select * from mybatis.user
  </select>
</mapper>


```

**测试：**

junit测试：

注意：测试写在test文件夹绿色的java文件夹下，而且绿java最好和上面的蓝java对应

```java
package com.wang.dao;

import com.wang.pojo.User;
import com.wang.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test() {

        // 第一步：获得 sqlSession 对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 第二步：执行 SQL
        // 方式一： getMapper (推荐使用该方式)
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();

        // 方式二 (不推荐使用该方式)
        //   List<User> userList = sqlSession.selectList("com.kuang.dao.UserDao.getUserList");

        for (User user : userList) {//遍历数组
            System.out.println(user);
        }

        // 关闭 sqlSession
        sqlSession.close();

    }
}

```

**小结**：Mybatis能够实现要注意几个点：

dao包下的Mapper.xml配置文件中，namespace需要和对应的接口名一致

增删改查（CRUD）：

select 选择：

id对应namespace中的方法名；

resultType：sql执行的返回值，（个人理解就是数据库查询后将结果传递给User这个类）

parameterType：参数类型

```xml
<mapper namespace="com.wang.dao.UserDao">
<!--select 查询语句-->
<select id="getUserList" resultType="com.wang.pojo.User">
        select * from mybatis.user;
    </select>
</mapper>
```

现在一般只需要修改dao下的接口和xml文件，测试类这三处

now，我们把上面代码中的User替换为UserMapper，这样更符合Mybatis的直观规范

```java
//现在一个根据ID查询用户的类如下
package com.wang.dao;
import com.wang.pojo.User;
import java.util.List;

public interface UserMapper {//Dao也就是mapper的含义

    List<User> getUserList();//查询全部用户
    //根据id查询用户
    User getUserById();

}

```

然后在UserMapper这个xml文件中继续添加标签

```xml
<select id="getUserById" parameterType="int" resultType="com.wang.pojo.User">
    
    select * from mybatis.user where id = #{id}
</select>    
    
```

然后补充测试类：

```java
package com.wang.dao;

import com.wang.pojo.User;
import com.wang.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test() {

        // 第一步：获得 sqlSession 对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 第二步：执行 SQL
        // 方式一： getMapper (推荐使用该方式)
        UserMapper userDao = sqlSession.getMapper(UserMapper.class);
        List<User> userList = userDao.getUserList();

        for (User user : userList) {
            System.out.println(user);
        }

        // 关闭 sqlSession
        sqlSession.close();

    }
   @Test
    public void getUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();//获取执行sql的对象

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(1);//用mapper这个对象去调用方法,此处是对应查询id参数=1的用户信息
        System.out.println(user);


        sqlSession.close();
    }
    
}

```

这样就可以查询id=1的用户的信息

//增删改 需要提交事务,就好像jdbc中的commit方法一样 sqlSession.close();

Insert 插入：

要明白，规定增删改查时方法的参数是什么，针对用户操作（那就是表名user

```java
int addUser(User user);
```

标签内的id对应方法名字，参数类型是类名，结果返回值是int类型，可以不写

```xml
<!--对象中的属性，可以直接取出来 -->
<insert id="addUser"  parameterType="com.wang.pojo.User">
    insert into mybatis.user(id,name,pwd) values(#{id},#{name},#{pwd})
</insert>
```

然后添加测试类中的方法

```java
public void addUser(){
		SqlSession sqlSession = MybatisUtils.getSqlSession();//获取执行sql的对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    	int r = mapper.addUser(new User(4,"啪啪","233233"));
		if(r>0){
            System.out.println("插入成功，其实是句废话");
        }
    //增删改 需要提交事务,就好像jdbc中的commit方法一样
    	sqlSession.commit();
    
        sqlSession.close();


}
```

同理update 更新：

int updateUser(User user);

```xml
    <update id="updateUser"  parameterType="com.wang.pojo.User">
    update mybatis.user set name=#{name},pwd=#{pwd}  where id =#{id};
</update>
```

```java
@Test
    public void updateUser() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();//获取执行sql的对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        mapper.updateUser(new User(4, "jojo", "666666"));

        //增删改 需要提交事务,就好像jdbc中的commit方法一样
        sqlSession.commit();

        sqlSession.close();

    }


```

delete删除：

```xml
<delete id="deleteUser"  parameterType="int">
delete from mybatis.user  where id = #{id};
</delete>
```

测试类中：

```java
   public void deleteUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();//获取执行sql的对象

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.deleteUser(4);
       
        sqlSession.commit();


        sqlSession.close();
    }
```

另外，debug时报错信息后面的比较有用

当数据库中表的字段或者参数过多时还有一种方法：MAP方法

```java
public void addUser2(){
		SqlSession sqlSession = MybatisUtils.getSqlSession();//获取执行sql的对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    	Map<String, Object> map = new HashMap<String,Object>();
    	// mapper.addUser(new User(4,"啪啪","233233"));
		map.put("userid",5);

    	map.put("passWord","233233");
    	mapper.addUser2(map);
    //增删改 需要提交事务,就好像jdbc中的commit方法一样
    	sqlSession.commit();
    
        sqlSession.close();


}

```

//这种方法可以避免像常规方法那样需要把所有参数写出来

```xml

<insert id="addUser"  parameterType="map">
    insert into mybatis.user(id,pwd) values(#{userid},#{passWord})
</insert>
```

#### 模糊查询

java代码执行的时候，传递通配符% %，%表示0或多个连续字符

```java
List<User> userlist = mapper.getUserLike("%李%");
```

并在sql拼接中使用通配符

```xml
select *from mybatis.user where name like "%"#{value}"%"
```





#### 配置解析

1.Mybatis中有两种类型的事务管理器：JDBC和Managed，虽然后者几乎不会使用

2.另外，mybatis-config中环境配置一般会有两个，一个正式的，一个测试的

至于程序选择哪个，需要看defult的值(默认)

```xml
 <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
     
        <environment id="test">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
```

3.当spring+mybatis时完全不需要配置事务管理器

默认的事务管理器是JDBC，连接池：POOLED

4.属性properties

db.properties:

注意：和xml中相比 & 这个符号不需要加amp;了

```
driver=com.mysql.jdbc.Driver

url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8

username=root

password=password
```

```xml
<properties resource="db.properties"/>
```

如果做了以上配置，那么环境配置中value需要做出更改

```xml
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
```

#### 类型别名（typeAliases）

可以通过注解来为javabean赋予类的别名

@Alias("hello")

注意是ibatis包下的Alias

也可以在my-batis中设置,放在第三层

定义后每次回自动扫描，将类名小写作为其别名

```
   <typeAliases>
        <package name="com.wang.pojo"/>
    </typeAliases>
```

**resultsetMap结果集映射：**

除了起别名，利用结果集映射也可以解决属性名和字段名不一致的问题

(比如User中是password

但是数据库中是pwd),这样查询时会出现null

所以通过添加resultMap和result标签来设置列名和属性名对应

```xml

<!--select 查询语句-->
<resultMap id="UserMap" type = "User">
    <!-- column库中字段，property实体类中的属性-->
    <result column="id" property="id"/>
     <result column="name" property="name"/>
     <result column="pwd" property="password"/>
</resultMap>
  <select id="getUserById" parameterType="int" resultMap="UserMap">

    select * from mybatis.user where id = #{id}
</select>

```

//select id,name,pwd from mybatis.user where id = #{id}

#### 日志文件与LOG4J

如果一个数据库操作出现异常，排错时日志可以帮到你

日志工厂：主要学习LOG4J和STDOUT——LOGGING

在配置文件xml中加入如下语句（位置在properties下，也就是第二行）

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

运行后会在控制台出现日常输出结果以及新出现的日志信息，如下

```
E:\Environment\jdk1.8\bin\java.exe...
PooledDataSource forcefully closed/removed all connections.
PooledDataSource forcefully closed/removed all connections.
PooledDataSource forcefully closed/removed all connections.
PooledDataSource forcefully closed/removed all connections.
Opening JDBC Connection
Created connection 1541049864.
Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5bda8e08]
==>  Preparing: select * from mybatis.user; 
==> Parameters: 
<==    Columns: id, name, pwd
<==        Row: 1, 疯帽, 123456
<==        Row: 2, 大范, 123456
<==        Row: 3, 黑炭, 123886
<==      Total: 3
User{id=1, name='疯帽', pwd='123456'}
User{id=2, name='大范', pwd='123456'}
User{id=3, name='黑炭', pwd='123886'}
Resetting autocommit to true on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5bda8e08]
Closing JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5bda8e08]
Returned connection 1541049864 to pool
```

LOG4J的话在values更改且需要导入新的包：

```xml
<settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
```

//

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

```

然后我们可以通过一个配置文件log4j.properties来灵活配置



```properties
# 配置rootLogger为DEBUG级别，将日志信息输出到console和file两个目的地这两者的定义在下面
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

LOG4J的简单使用

除了控制台运行外，

在测试类(因为要在这里使用log4j)中新建函数:

```java

//可以放在函数外面需要提升一下作用域，static
static  Logger logger = Logger.getLogger(UserDaoTest.class);
```

```
import org.apache.log4j.Logger;
```

然后

```java
    @Test
    public void testLog4j(){
        logger.info("info:进入了log4j方法");
        logger.debug("debug:进入了log4j方法");
        logger.error("error:进入了log4j方法");
    }
```

然后发现项目的路径log4j.appender.file.File=./log/wang.log下有新的日志文件生成

#### 分页

分页可以减少数据的处理量；

使用limit分页：

```xml
SELECT * from user limit 0,2;
//如果只有一个数字2，那就只输出两个
```

使用mybatis实现分页：

思路：

1.接口

2.Mapper.xml

3.测试

以查询为例

```java
//在UserMapper接口中定义一个方法
 List<User> getUserByLimit(Map<String,Integer>map);//分页至少需要两个参数

```

然后去UserMapper.xml中实现方法

```xml
<!--分页 -->
<select id="getUserByLimit" parameterType="map" resultType="user">

    select * from mybatis.user limit #{startIndex},#{pageSize}
</select>

   
```

最后去测试类定义测试方法

```java
    @Test
    public void getUserByLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        HashMap<String, Integer> map = new HashMap<String, Integer>();
        map.put("startIndex",0);
        map.put("pageSize",2);

        List<User> userList = mapper.getUserByLimit(map);
        for (User user : userList) {
           System.out.println(user);
            
        }
        
        sqlSession.close();
    }
```

关于使用resultType时有个问题需要注意：如果你已经在my-batis中设置了别名

<typeAliases>
        <package name="com.wang.pojo"/>
    </typeAliases>

那么resultType="xxx"的时候要用别名，也就是小写得到实体类名，不然请用路径名



#### 使用注解开发（不过一般除了mybatis才是注解开发）

面向接口编程：根本原因是解耦

接口应有两类：第一类是对一个个体的抽象，它可对应为一个抽象体

第二类是对一个个体某一方面的抽象，形成一个抽象面



面向对象与面向过程：

面向对象是指考虑问题时以对象为单位，考虑他的属性和方法，

面向过程是指主要考虑以一个具体的流程为单位，考虑它的实现



关于使用注解开发：

如果不使用注解，应该在dao下是UserMapper接口和一个xml文件

如果使用注解开发：

```java
public interface UserMapper{
    @Select("select * from user")
    List<User> getUsers();
}
```

然后在配置文件mybatis（而不是UserMapper.xml）中绑定接口：

```xml
    <mappers>
    <mapper class="com.wang.dao.UserMapper"/>
    </mappers>
```

然后在测试类写测试方法

```java
@Test
public void test(){

        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> users = mapper.getUsers();
        for (User user : users) {
            System.out.println(user);

        }

        sqlSession.close();
    
}
```



#### 注解与CRUD



我们可以在工具类创建的时候实现自动提交事务！

```java
public static SqlSession  getSqlSession(){
    return sqlSessionFactory.openSession(true);
}
```



编写接口，增加注解

```java
public interface UserMapper {

    @Select("select * from user")
    List<User> getUsers();

    // 方法存在多个参数，所有的参数前面必须加上 @Param("id")注解
    @Select("select * from user where id = #{id}")
    User getUserByID(@Param("id") int id);


    @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
    int addUser(User user);

    
    @Update("update user set name=#{name},pwd=#{password} where id = #{id}")
    int updateUser(User user);

    
    @Delete("delete from user where id = #{uid}")
    int deleteUser(@Param("uid") int id);
}
```



测试类

【注意：我们必须要将接口注册绑定到我们的核心配置文件中！】

```xml
@Test
public void test(){
//这里是以更新语句为例
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        mapper.updateUser(new User(5,"to","233332"));
 //这里是设置了自动提交所以没写commit，但是实际编写不要轻易设置自动提交

        sqlSession.close();
    
}
```



**关于@Param() 注解**

- 基本类型的参数或者String类型，需要加上
- 引用类型不需要加
- 如果只有一个基本类型的话，可以忽略，但是建议大家都加上！
- 我们在SQL中引用的就是我们这里的 @Param() 中设定的属性名！



**#{}     ${} 区别**



## Lombok

```java
Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.
```

- java library
- plugs
- build tools
- with one annotation your class



使用步骤：

1. 在IDEA中安装Lombok插件！

2. 在项目中导入lombok的jar包

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.10</version>
   </dependency>
   ```

3. 在实体类上加注解即可！

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   ```



```java
@Getter and @Setter
@FieldNameConstants
@ToString
@EqualsAndHashCode
@AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger
@Data
@Builder
@Singular
@Delegate
@Value
@Accessors
@Wither
@SneakyThrows
```

说明：

```
@Data：无参构造，get、set、tostring、hashcode，equals
@AllArgsConstructor
@NoArgsConstructor
@EqualsAndHashCode
@ToString
@Getter
```



## 10、多对一处理

多对一：

![1569909163944](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569909163944.png)

- 多个学生，对应一个老师
- 对于学生这边而言，  **关联** ..  多个学生，关联一个老师  【多对一】
- 对于老师而言， **集合** ， 一个老师，有很多学生 【一对多】

![1569909422471](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569909422471.png)

SQL：

```sql
CREATE TABLE `teacher` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师'); 

CREATE TABLE `student` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  `tid` INT(10) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fktid` (`tid`),
  CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');

```



### 测试环境搭建

1. 导入lombok
2. 新建实体类 Teacher，Student
3. 建立Mapper接口
4. 建立Mapper.XML文件
5. 在核心配置文件中绑定注册我们的Mapper接口或者文件！【方式很多，随心选】
6. 测试查询是否能够成功！



### 按照查询嵌套处理

```xml
<!--
    思路:
        1. 查询所有的学生信息
        2. 根据查询出来的学生的tid，寻找对应的老师！  子查询
    -->

<select id="getStudent" resultMap="StudentTeacher">
    select * from student
</select>

<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <!--复杂的属性，我们需要单独处理 对象： association 集合： collection -->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{id}
</select>

```



### 按照结果嵌套处理

```xml
<!--按照结果嵌套处理-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname
    from student s,teacher t
    where s.tid = t.id;
</select>

<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
    </association>
</resultMap>
```



回顾Mysql 多对一查询方式：

- 子查询
- 联表查询



## 11、一对多处理

比如：一个老师拥有多个学生！

对于老师而言，就是一对多的关系!



### 环境搭建



1. 环境搭建，和刚才一样

**实体类**

```java
@Data
public class Student {

    private int id;
    private String name;
    private int tid;

}

```

```java
@Data
public class Teacher {
    private int id;
    private String name;

    //一个老师拥有多个学生
    private List<Student> students;
}
```





### 按照结果嵌套处理

```xml
    <!--按结果嵌套查询-->
    <select id="getTeacher" resultMap="TeacherStudent">
        select s.id sid, s.name sname, t.name tname,t.id tid
        from student s,teacher t
        where s.tid = t.id and t.id = #{tid}
    </select>

    <resultMap id="TeacherStudent" type="Teacher">
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
        <!--复杂的属性，我们需要单独处理 对象： association 集合： collection
        javaType="" 指定属性的类型！
        集合中的泛型信息，我们使用ofType获取
        -->
        <collection property="students" ofType="Student">
            <result property="id" column="sid"/>
            <result property="name" column="sname"/>
            <result property="tid" column="tid"/>
        </collection>
    </resultMap>
```



### 按照查询嵌套处理

```xml
<select id="getTeacher2" resultMap="TeacherStudent2">
    select * from mybatis.teacher where id = #{tid}
</select>

<resultMap id="TeacherStudent2" type="Teacher">
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudentByTeacherId" column="id"/>
</resultMap>

<select id="getStudentByTeacherId" resultType="Student">
    select * from mybatis.student where tid = #{tid}
</select>
```



### 小结

1. 关联 - association   【多对一】
2. 集合 - collection   【一对多】
3. javaType    &   ofType
   1. JavaType  用来指定实体类中属性的类型
   2. ofType  用来指定映射到List或者集合中的 pojo类型，泛型中的约束类型！



注意点：

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一中，属性名和字段的问题！
- 如果问题不好排查错误，可以使用日志 ， 建议使用 Log4j



**慢SQL       1s        1000s**      

面试高频

- Mysql引擎
- InnoDB底层原理
- 索引
- 索引优化！





## 12、动态 SQL

==**什么是动态SQL：动态SQL就是指根据不同的条件生成不同的SQL语句**==

利用动态 SQL 这一特性可以彻底摆脱这种痛苦。

```xml
动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

if
choose (when, otherwise)
trim (where, set)
foreach
```



### 搭建环境

```sql
CREATE TABLE `blog` (
  `id` varchar(50) NOT NULL COMMENT '博客id',
  `title` varchar(100) NOT NULL COMMENT '博客标题',
  `author` varchar(30) NOT NULL COMMENT '博客作者',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `views` int(30) NOT NULL COMMENT '浏览量'
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```



创建一个基础工程

1. 导包

2. 编写配置文件

3. 编写实体类

   ```java
   @Data
   public class Blog {
       private int id;
       private String title;
       private String author;
       private Date createTime;
       private int views;
       
       
   }
   ```

4. 编写实体类对应Mapper接口 和 Mapper.XML文件



### IF

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog where 1=1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```

### choose (when, otherwise)

```xml
    <select id="queryBlogChoose" parameterType="map" resultType="blog">
        select * from mybatis.blog
        <where>
            <choose>
                <when test="title != null">
                    title = #{title}
                </when>
                <when test="author != null">
                    and author = #{author}
                </when>
                <otherwise>
                    and views = #{views}
                </otherwise>
            </choose>
        </where>
    </select>
```



### trim (where,set)

```xml
select * from mybatis.blog
<where>
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</where>
```

```xml
<update id="updateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id}
</update>

```

==**所谓的动态SQL，本质还是SQL语句 ， 只是我们可以在SQL层面，去执行一个逻辑代码**==

if

where ， set  ， choose ，when





### SQL片段

有的时候，我们可能会将一些功能的部分抽取出来，方便复用！

1. 使用SQL标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
       <if test="title != null">
           title = #{title}
       </if>
       <if test="author != null">
           and author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方使用Include标签引用即可

   ```xml
   <select id="queryBlogIF" parameterType="map" resultType="blog">
       select * from mybatis.blog
       <where>
           <include refid="if-title-author"></include>
       </where>
   </select>
   ```

   

注意事项：

- 最好基于单表来定义SQL片段！
- 不要存在where标签



### Foreach

```sql
select * from user where 1=1 and 

  <foreach item="id" collection="ids"
      open="(" separator="or" close=")">
        #{id}
  </foreach>

(id=1 or id=2 or id=3)

```

![1569979229205](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569979229205.png)

![1569979339190](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569979339190.png)

```xml
<!--
        select * from mybatis.blog where 1=1 and (id=1 or id = 2 or id=3)

        我们现在传递一个万能的map ， 这map中可以存在一个集合！
-->
<select id="queryBlogForeach" parameterType="map" resultType="blog">
    select * from mybatis.blog

    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>

</select>

```



==动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了==

建议：

- 现在Mysql中写出完整的SQL,再对应的去修改成为我们的动态SQL实现通用即可！



## 13、缓存 （了解）

### 13.1、简介

```
查询  ：  连接数据库 ，耗资源！
	一次查询的结果，给他暂存在一个可以直接取到的地方！--> 内存 ： 缓存
	
我们再次查询相同数据的时候，直接走缓存，就不用走数据库了
```



1. 什么是缓存 [ Cache ]？

   - 存在内存中的临时数据。
   - 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

2. 为什么使用缓存？

   - 减少和数据库的交互次数，减少系统开销，提高系统效率。

3. 什么样的数据能使用缓存？

   - 经常查询并且不经常改变的数据。【可以使用缓存】

     

### 13.2、Mybatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）

  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。

  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

    

### 13.3、一级缓存

- 一级缓存也叫本地缓存：  SqlSession
  - 与数据库同一次会话期间查询到的数据会放在本地缓存中。
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；



测试步骤：

1. 开启日志！
2. 测试在一个Sesion中查询两次相同记录
3. 查看日志输出

![1569983650437](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569983650437.png)



缓存失效的情况：

1. 查询不同的东西

2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存！

   ![1569983952321](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569983952321.png)

3. 查询不同的Mapper.xml

4. 手动清理缓存！

   ![1569984008824](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569984008824.png)



小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段！

一级缓存就是一个Map。




### 13.4、二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中；



步骤：

1. 开启全局缓存

   ```xml
   <!--显示的开启全局缓存-->
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存的Mapper中开启

   ```xml
   <!--在当前Mapper.xml中使用二级缓存-->
   <cache/>
   ```

   也可以自定义参数

   ```xml
   <!--在当前Mapper.xml中使用二级缓存-->
   <cache  eviction="FIFO"
          flushInterval="60000"
          size="512"
          readOnly="true"/>
   ```

3. 测试

   1. 问题:我们需要将实体类序列化！否则就会报错！

      ```
      Caused by: java.io.NotSerializableException: com.kuang.pojo.User
      ```



小结：

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会先放在一级缓存中；
- 只有当会话提交，或者关闭的时候，才会提交到二级缓冲中！





### 13.5、缓存原理

![1569985541106](C:\Users\dell\Desktop\Typora专属图片文档\Mybatis\Mybatis课堂笔记.assets\1569985541106.png)



### 13.6、自定义缓存-ehcache

```xml
Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存
```

要在程序中使用ehcache，先要导包！

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.1.0</version>
</dependency>
```

在mapper中指定使用我们的ehcache缓存实现！

```xml
<!--在当前Mapper.xml中使用二级缓存-->
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <!--
       diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
       user.home – 用户主目录
       user.dir  – 用户当前工作目录
       java.io.tmpdir – 默认临时文件路径
     -->
    <diskStore path="./tmpdir/Tmp_EhCache"/>
    
    <defaultCache
            eternal="false"
            maxElementsInMemory="10000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="259200"
            memoryStoreEvictionPolicy="LRU"/>
 
    <cache
            name="cloud_user"
            eternal="false"
            maxElementsInMemory="5000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="1800"
            memoryStoreEvictionPolicy="LRU"/>
    <!--
       defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
     -->
    <!--
      name:缓存名称。
      maxElementsInMemory:缓存最大数目
      maxElementsOnDisk：硬盘最大缓存个数。
      eternal:对象是否永久有效，一但设置了，timeout将不起作用。
      overflowToDisk:是否保存到磁盘，当系统当机时
      timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
      timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
      diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
      diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
      diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
      memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
      clearOnFlush：内存数量最大时是否清除。
      memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
      FIFO，first in first out，这个是大家最熟的，先进先出。
      LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
      LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
   -->

</ehcache>

```



Redis数据库来做缓存！  K-V







**mybatis总结**



**mybatis执行流程:**

Resources获取加载全局配置文件  -->>

实例化SqlSessionFactoryBuilder构造器（）-->

解析文件流ConfigBuilder---->

Configruation所有的配置信息----->

实例化SqlSessionFactory--->

transactional事务管理器-->

创建executor执行器---->

创建sqlSession--->

实现CRUD（增删改查）----->可回滚至transactional事务管理器

​										 ------>也可以查看是否执行成功，不成功也可回滚

-->提交事务--->关闭



从代码层面来讲就是：

在工具类中加载全局配置文件和文件流是并实例化SqlSessionFactory,

sqlSession去实现CRUD，（建立接口并用xml去实现方法）



PS：项目中子模块mybatis-02没有设置别名，mybatis-03设置了别名和日志

而mysql8.0时区问题需要在my.ini（这个文件一般在programdata隐藏文件夹下

中加入修改（百度即可）

Utils工具类：加载和定义配置文件（实际配置文件mybatis-config在resources下）

myBatisPlus：分页插件，简化增删改查，(mapper也是这样)

注意接口和xml文件尽量同名，而且在同一个包下；

















​	Mybatis 

未完成任务：
	java 多线程
	java集合

​	Spring

​	Springboot
​	SpringCloud

​	SpringMVC	