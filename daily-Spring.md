### 01.16-01.23 Spring学习

#### 简介：

Spring是一个轻量级控制反转（IoC）和面向切面（AOP）的容器框架

目的是使现有的技术更加容易使用，简化开发

SSH：struct2Spring+Hibernate(和ssm比较旧了

SSM：SpringMvc+ Spring+mybatis

全部版本spring的下载地址：

https://repo.spring.io/release/org/springframework/spring/

当然在官网点小猫猫图标也可以在github下载

在maven下载spring MVC的依赖，以及整合mybatis的Spring-jdbc

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

```

**优点：**

Spring是一个开源的免费框架（容器）

Spring是一个 轻量级的，（顺便一提mybatis也是轻量级的），非入侵式的框架（导入不会破坏项目）

<u>控制反转（IOC），面向切面编程（AOP）</u>

支持事务的处理，对框架整合的支持；



#### Spring组成：

Spring AOP + Spring ORM +Spring DAO + Spring Web

+Spring Context +Spring Web MVC  + Spring Core



**拓展：**

Spring Boot

+ 一个快速开发的脚手架
+ 基于SpringBoot可以快速的开发单个微服务

Spring Cloud

+ SpringCloud是基于SpringBoot实现的



SpringBoot的出现是因为Spring后来配置很复杂 （配置地狱hh）



#### IOC理论

按照原来的java逻辑：

需要Dao层：UserDao接口和UserDaoImpl实现类

需要业务层service：UserService接口和UserServiceImpl实现类

这样的话每次业务层都需要建立对象去调用dao的方法，很麻烦；



在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码，这很麻烦；

我们用一个set接口实现：

```java
 private UserDao userDao;
    //利用set进行动态实现值的注入

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

```

使用set注入后，程序不再具有主动性，而是被动接收

也就是我们不需要去new对象，而改为用户选择set的具体对象，对象创建由set方法自动完成

这就是IOC设计思想（控制反转）。

把主动权交给用户，由用户选择调用哪种业务然后进入业务层

个人认为：所谓控制反转就是获得依赖对象的方式反转了；

DI（依赖注入）是实现IOC的一种方法；



第一个程序HelloSpring

注意！！打开idea创建新项目一定要在setting下看看meven路径是否正确，除非你想让c盘爆红

```xml
    <!-- 使用Spring创建对象，在Spring里这些都成为bean-->
    <bean id="Hello" class="com.wang.pojo.Hello">
        <property name="str" value="Spring"/>
        <!-- collaborators and configuration for this bean go here -->
    </bean>
```



当然实体类还是需要的

```java
package com.wang.pojo;

public class Hello {

    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}

```

然后是测试类：

```java
 //获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //对象也就是bean已经在spring中配置了（xml中），使用要取出
        Hello hello = (Hello) context.getBean("Hello");//需要强制转换一哈
        System.out.println(hello.toString());
```

Hello对象是如何创建的？其实hello是由Spring创建的；

bean 就是对象，bean中的id就是变量名，class是个路径，等价于new的对象

property就是给对象中的属性设置一个值

这个过程就成为控制反转，与传统不同，我们不需要手动new对象了

<u>控制反转：就是xxx控制对象的创建，xxx从程序本身变为了Spring</u>

<u>反转：程序本身不创建对象，改为被动的接收对象</u>



所以，基本上只需要在xml中修改就好

#### IOC的其他创建对象方法：

有参构造：

- 下标赋值

```xml
 <bean id="user" class="com.wang.pojo.User">

        <constructor-arg index="0" value="肠粉龙"/>

        <!-- collaborators and configuration for this bean go here -->
    </bean>
```

User：

```java
package com.wang.pojo;

import org.springframework.http.converter.json.GsonBuilderUtils;

public class User {
    private String name;

    public User(String name){
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    public void show() {
        System.out.println("name="+name);
    }
}

```

下标0就是User方法中的第一个属性；

第二种:通过类型创建:type；（不建议使用）

第三种:直接通过参数名来设置

```xml
<constructor-arg name="name" value ="老王"/> 
```



另外在创建bean之前，就构造方法就已经实例化了，（可以自行创建第二个无参方法和有参方法做对比，发现 有没有调用都会实例化；

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了

**Spring配置：**

别名：

```xml
<alias name="user" alias="jojo"/>
```

测试类中

```java

    public static void main(String[] args) {
//获取applicationcontext
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //拿到了spring容器
        User user = (User) context.getBean("jojo");
        user.show();
    }
```

这样设置jojo就可以代替user，别名一般用于代替包名特别长的情况

bean的配置：

id ：bean的唯一标识符；class：bean对象对应的路径或者全名

name：也可以起别名，还可以起多个别名，用逗号分隔

```xml
<bean id="xxx" class="xxxxx" name="u1,ac">

</bean>
```

import：

一般用于团队开发：他可以将多个配置文件导入合并为一个

```xml
<import recourse="beans.xml"/>
<import recourse="beans2.xml"/>
<import recourse="beans3.xml"/>
<!--如果再applicationContext.xml这个文件中加入上述语句，就相当于把这几个xml都导入了 -->
```



#### DI（依赖注入）



构造器注入：（构造方法）

`<constructor-arg name="age" value="21"></constructor-arg>`



<u>set方式注入：（重点）</u>

- 依赖注入：Set注入，

  依赖：bean对象的创建依赖于容器！

  注入：bean对象中的所有属性，由容器来注入



环境搭建：

- 复杂类型（bean | ref | idref | list | set | map | props | value | null）
- 真实测试对象

注意：写测试类创建new ClassPathXmlApplicationContext()

快捷方式为new CPX，就会自动出现啦

复杂类型：

```java
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}

```

真实对象：

```java
package com.wang.pojo;

import java.util.*;

public class Student {

    private  String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String,String> card;
    private Set<String> games;
    private String wife;
    private Properties info;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String[] getBooks() {
        return books;
    }

    public void setBooks(String[] books) {
        this.books = books;
    }

    public List<String> getHobbys() {
        return hobbys;
    }

    public void setHobbys(List<String> hobbys) {
        this.hobbys = hobbys;
    }

    public Map<String, String> getCard() {
        return card;
    }

    public void setCard(Map<String, String> card) {
        this.card = card;
    }

    public Set<String> getGames() {
        return games;
    }

    public void setGames(Set<String> games) {
        this.games = games;
    }

    public String getWife() {
        return wife;
    }

    public void setWife(String wife) {
        this.wife = wife;
    }

    public Properties getInfo() {
        return info;
    }

    public void setInfo(Properties info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", address=" + address +
                ", books=" + Arrays.toString(books) +
                ", hobbys=" + hobbys +
                ", card=" + card +
                ", games=" + games +
                ", wife='" + wife + '\'' +
                ", info=" + info +
                '}';
    }
}

```

beans.xml

```xml
    <bean id="student" class="com.wang.pojo.Student">
        <property name="name" value="钢蛋儿"/>

        <!-- collaborators and configuration for this bean go here -->
    </bean>
```

测试类：

```java
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        Student student = (Student) context.getBean("student");
        System.out.println(student.getAddress());
```

为剩下的属性依次注入(map，list，Set之类的)：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 使用Spring创建对象，在Spring里这些都成为bean-->

    <bean id="address" class="com.wang.pojo.Address">
        <!--第二种就是bean注入用ref引用 -->
        <property name="address" value="北京"/>
    </bean>
    <bean id="student" class="com.wang.pojo.Student">
        <!-- 第一种value值注入-->
        <property name="name" value="钢蛋儿"/>
        <!--第二种就是bean注入用ref引用 -->
        <property name="address" ref="address"/>

        <!--第三种是数组注入用ref引用 -->
        <property name="books">

            <array>
                <value>西游记</value>
                <value>三国演义</value>
                <value>水浒传</value>
                <value>红楼梦</value>
            </array>
        </property>
        <!-- List-->
        <property name="hobbys">
            <list>
                <value>篮球</value>
                <value>羽毛球</value>
                <value>手绘</value>
            </list>
        </property>

        <!--map -->
        <property name="card">
            <map>
                <entry key="身份证" value="2435454423566"/>
                <entry key="银行卡" value="1119299292929"/>
            </map>
        </property>
        <!-- Set-->
        <property name="games">
            <set>
                <value>mc</value>
                <value>wows</value>
                <value>l4d2</value>
            </set>
            <!--null -->
        </property>
        <property name="wife">
        <null/>
        </property>

        <property name="info">
            <props>
                <prop key="driver">24553</prop>
                <prop key="url">xxx</prop>
                <prop key="username">猪八</prop>
                <prop key="password">233</prop>

            </props>
        </property>

        <!-- collaborators and configuration for this bean go here -->
    </bean>



    <!-- more bean definitions go here -->

</beans>
```

#### bean的作用域（beanScope)

singleton：（单例）对象全局唯一，单例模式：只创建一个bean实例，

可供多个<bean></bean>去引用，全局共享一个

```
<bean id="user2" class="com.wang.pojo.User" scope="singleton">
//默认是单例模式
```

sprototype：原型：表示每一个bean形成的对象都是单独的

剩下四个只能在web开发中使用

request：只在本次请求中有效

session：当次会话（服务）有效，也可以理解为浏览器不关闭时一直是有效的

application：全局存活

websocket：

这六种以前两种为主；

单例模式远比原型模式节省资源，只需要共享一个实例就好，不过并发下单例可能会产生延迟和数据不一致

复习单例模式：

**单例模式**

构造器私有，别人无法new对象所以保证内存中只有一个对象

饿汉式单例：先创建对象；饿汉式容易浪费内存

```java
public class Hungry{
    
    
    private Hungry(){}
    
    private final static Hungry hungry = new Hungry();
    public static Hungry getInstance(){
        return hungry;
    }
}
```

懒汉式：用的时候才创建对象

```java
public class LazyMan{
    
    private LazyMan(){}//构造器私有
    private static LazyMan lazyMan; 
    
    public static LazyMan getInstance(){
        if(LazyMan==null//用的时候才去检查有没有实例，如果有则返回，没有则新建
            lazyMan = new LazyMan();
        }
        return lazyMan;
    }
}
```

但是在并发下懒汉式有问题，所以我们可以加上锁，进行两次检测

也称DCL懒汉式

```java
//双重检测锁模式
public class LazyMan{
    
    private LazyMan(){
      //  System.out.println(Thread.currentThread().getName()+"ok");
    }//构造器私有
    private volatile static LazyMan lazyMan; 
    
    public static LazyMan getInstance(){
        if(lazyMan==null){
            synchronized(LazyMan.class){
        		if(LazyMan==null){//用的时候才去检查有没有实例，如果有则返回，没有则新建
            	lazyMan = new LazyMan();
        		}
         	}
        
        }
        return lazyMan;
    }
    
    /*
    public static void main(String[] args){
    for(int i=0;i<10;i++){
    new Thread(()->{
    LazyMan.getInstance();
    	}).start();
    }
    }
    
    */
    
}
```

双检锁，又叫双重校验锁，综合了懒汉式和饿汉式两者的优缺点整合而成。看上面代码实现中，特点是在synchronized关键字内外都加了一层 if 条件判断，这样既保证了线程安全，又比直接上锁提高了执行效率，还节省了内存空间

**不过	lazyMan = new LazyMan();**

**这段语句不是原子性操作，**

其执行步骤：

1分配内存空间

2执行构造方法，初始化对象

3把对象指向这个空间，

但是线程顺序是不一定有序的，

123,132，这个顺序都有可能，

倘若132，会导致判断非空而直接return lazyMan

此时lazyMan还没有完成构造，就会出现问题

所以为了安全，避免指令重排，需要增加关键字：

```java
private volatile static LazyMan lazyMan; 
```

除了这三种，还有静态内部类方式：xxxx



**关于枚举：**（enum）
枚举本身也是一个class类，从jdk1.5出现

比如public enum xxx{

}这个类

反射无法破坏枚举

和单例相反，枚举是多例，不过实例的个数是有限的，

枚举类中实例为枚举项



话归正题：

#### **bean自动装配**(Autowired)

是Spring满足bean的依赖的一种方式，

在Spring中有三种装配的方式：

1.在xml中配置

2.在java中显示配置

3.隐式 的自动装配bean（这种）

新建一个子项目：spring-05



一个人有猫和狗两只宠物：

```java
    @Test
    public void Test01(){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Person person = context.getBean("person", Person.class);
        person.getDog().shout();
        person.getCat().shout();

    }

```

xml文件中加入autowired 来代替如下语句

```xml
        <property name="dog" ref="dog"/>
        <property name="cat" ref="cat"/>
```

byName会自动在容器中查找，和自己对象set方法后面的值对应的id

```xml
    <bean id="cat" class="com.wang.pojo.Cat"/>
    <bean id="dog" class="com.wang.pojo.Dog"/>
    <bean id="person" class="com.wang.pojo.Person" autowire="byName">

        <property name="name" value="鲁智深"/>

        
    </bean>
```

byType的话就是查找和自己对象属性类型相同的bean的id，也就是根据class来查找

好处就是不需要规定id都可以运行，但是如果出现dog2和dog3就不会执行了；

#### 注解实现自动装配

当然，要使用注解需要一些支持：

- 导入约束
- 配置注解的支持<context:annotation-config/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

```
@Autowired  //此注解为自动导入的意思
```

```
@Autowired 可以放在属性和set方法上
加入autowired注解后set方法都可以删去了，前提是自动装配的属性时一致的
```

@Nullable ：意为 被注解对象可为空

@Qualifier：指定匹配对象，配合Autowired使用

```java
@Autowired
@Qualifier(value="dog22")
private Dog dog;
//这样注解后如果出现 多个类型相同（也就是路径相同的bean，但是id不同，就会
//根据id找到对应的bean
//    <bean id="dog22" class="com.wang.pojo.Dog"/>
```

@Resource：也可以自动装配，属于非Spring所属注解，属于java，也就是不是Spring框架也能用 

resource会先匹配id是否相同，不同就去查找类型（路径），都不同才会报错

```java
@Resource(name="dog2")
private Dog dog;
```

两者区分：@Autowired 先按类型查找，如果多个就再按name（id查找，

@Resource  是先name后类型



#### Spring注解开发  *（现在注解开发较多，xml较少)

spring4之后 使用注解开发必须保证aop包导入

另外，使用注解需要导入context约束，增加注解支持

下面是子模块Spring-06-anno

在xml中删除下列bean语句

```
<bean id="user" class="com.wang.pojo.User"/>
```

改为如下语句

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

<!-- 指定要扫描的包，扫描这个包下的注解就会生效-->
    <context:component-scan base-package="com.wang.pojo"/>
    <context:annotation-config/>






    <!-- more bean definitions go here -->

</beans>
```

在User代码中加入注解：

```java
package com.wang.pojo;

import org.springframework.stereotype.Component;

//组件，相当于<bean id="user" class="com.wang.pojo.User"/>
@Component
public class User {
    public String name="跑得快的滑翔";

}
```

MyTest

```
import com.wang.pojo.User;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {

    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        //因为注解的原因，bean默认是注解下类名小写
        User user = (User) context.getBean("user");
        System.out.println(user.name);
    }
}
```

上述设置可以找到bean，但是无法直接给属性注解；

如下：把User中的改为public String name;然后在测试中加入@Value注解

```
@Component
public class User {
    @Value("傻洋")//相当于<property name="name",value="傻洋">
    public String name;

}
```

当然，@Value("xxx")也可以放在setName这种SET方法上；

**@Component还有衍生的注解**：在web开发中，按MVC分层

dao: @Repository  功能和Component一样，可用于区分各层次

service: @Service   同上

controller: @Controller

这四个注解功能一样，代表将某个类注册到Spring中，装配bean

**自动装配注解**：就是上一节学的@Autowired和@Qualifier @Resource @Notable



**作用域**：@Scope

@Scope("singlaton")，@Scope("Prototype")等等

**综上：**使用注解代替xml

注册bean改为@Component代替，

注入属性可用@Value("xxx")代替，

作用域可用@Scope代替

xml适用于任何场合，适合简单的项目

注解 ：不是自己类使用不了，而且出了问题维护很麻烦

市面上xml负责bean设置，属性注入交给注解实现；

注意一定要让注解生效

```
<!-- 指定要扫描的包，扫描这个包下的注解就会生效-->
    <context:component-scan base-package="com.wang.pojo"/>
    <context:annotation-config/>
```

#### JavaConfig（使用这个可以彻底摆脱xml配置全使用注解）

字面意思，完全使用java的方式配置Spring

现在是Spring4之后成为了一个核心功能；

新建子项目spring07

User类

```
package com.wang.pojo;

import org.springframework.beans.factory.annotation.Value;

public class User {
    private String name;

    public String getName() {
        return name;
    }
@Value("jojo")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

然后自己写一个wangConfig类作为配置

```
package com.wang.config;

import com.wang.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class wangConfig {
    @Bean
    public User getUser(){
        return new User();
    }


}
```

然后是测试类：

```
public static void main(String[] args) {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(wangConfig.class);
    User user = (User) context.getBean("getUser");
    System.out.println(user.getName());
}
```

事实证明:

@Conponment意思是说明此类已被spring接管,注册到容器中；

@Value依然是属性注入

@Configuration代表配置类，也就是beans.xml的代替；

@ComponentScan("com.wang.pojo")  指定扫描这个包

@Bean：@bean相当于<bean></bean>这个标签

```java
@Configuration
public class wangConfig {
    @Bean//相当于xml中的标签<bean>,方法名字getUser就相当于bean的id
    public User getUser(){//而方法的返回值就相当于bean的class
        return new User();//返回要注入到bean的对象
    }


}
```

当然还可以用import引入合并新的配置

```
@Configuration
@ComponentScan("com.wang.pojo")
@Import(wangConfig2.class)//引入了一个wang.Config2.class
```

javaConfig这种纯java的配置方式，在SpringBoot中很常见

#### 代理模式：

代理：就是代替别人做一些事情，代理模式是SpringAOP的底层

代理模式分类：

- 静态代理
- 动态代理

#### 静态代理模式：

![静态代理](C:\Users\dell\Desktop\Typora专属图片文档\images\静态代理.png)

角色分析：

抽象角色：一般使用接口或者抽象类来解决（比如共同租房等

真实角色：被代理的角色

代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作

客户：访问代理对象的人。

那么新建项目：07

房东Host，租客Client，代理Proxy，Rent出租接口

代理中介会做很多房东要做的事情，比如带你看房组，签合同之类的



代码步骤：

接口

```
package com.wang.demo01;

public interface Rent {//租房
    public void rent();
}
```

真实角色

```
package com.wang.demo01;
//房东
public class Host implements Rent{


    public void rent() {
        System.out.println("房东要出租房子");
    }
}
```

代理角色

```
package com.wang.demo01;

public class Proxy implements Rent{
    private  Host host;
    public Proxy(){}
    public Proxy(Host host){//这种方法可以用来完成对象的初始化
        this.host=host;
    }

    public void rent() {
        seeHouse();
        host.rent();
        hetong();
    }

    //中介带你看房
    public void seeHouse(){
        System.out.println("中介带你看房");
    }
    //中介带你签合同
    public void hetong(){
        System.out.println("中介带你签合同");
    }
}
```

客户端访问代理角色

```
package com.wang.demo01;

public class Client {//客户端，客户，委托人

    public static void main(String[] args) {
       //房东要租房子
        Host host = new Host();
        //host.rent();
        //代理 ，帮房东出租房子，但是会有一些附属操作
        Proxy proxy = new Proxy(host);
        //所以你不用找房东，直接面对中介即可
        proxy.rent();
    }
}
```

代理模式的好处：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
- 公共业务就交给代理角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理

缺点：

- 一个真实角色就会产生一个代理角色；代码量会翻倍，开发效率会变低



#### 深入理解静态代理：

本质上是为了不改变原有代码去增加一些东西，增强逻辑性

因为我们没有必要去改动原有的业务代码，开闭原则

一般程序流程：

dao->service->controller->前端

比如向原流程中横向加入代理类（横向开发），这就是切面思想，是AOP的实现

降低了耦合

#### 动态代理：

动态代理和静态代理角色一样

动态代理的代理类是动态生成，不是我们直接写好的，

动态代理分为两类：基于接口的动态代理，基于类的动态代理

- 基于接口：JDK动态代理
- 基于类：cglib
- java字节码实现：javasist



需要了解两个类：Proxy（代理），InvocationHandler(调用处理程序)，

handler处理

关于接口Interface InvocationHandler:

方法：invoke

其参数：

proxy

method

args

关于Proxy类：

提供了创建动态代理类和实例的静态方法

举例来说明：spring-08-proxy-demo03



通用代理类demo04

ProxyInvocationHandler类

```java
package com.wang.demo04;


import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//我们将会用这个类，自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {

    //被代理的接口
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    //生成代理类
    public Object getProxy(){//this指当前类的对象实例
        //下面方法里是三个参数
      return   Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }//getClassLoader()：获取该类的类加载器



    //
    //处理代理实例，并返回结果
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {


        log(method.getName());//用method就不需要写死方法，比如log("add");这样

        //动态代理的本质就是使用反射机制，比如得到代理类，得到代理方法等
        Object result = method.invoke(target, args);

        return result;
    }
    public void log(String msg){
        System.out.println("执行了"+msg+"方法");
    }
}
```

客户（测试）类

```java
package com.wang.demo04;


import com.wang.demo02.UserService;
import com.wang.demo02.UserServiceImpl;

public class Client {
    public static void main(String[] args) {
        //真实角色
        UserService userService = new UserServiceImpl();

        //代理角色，暂时不存在，需要找代理程序去处理
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        pih.setTarget(userService);//代理一个接口,也就是真实对象
        //然后就是动态生成代理类
       UserService proxy = (UserService) pih.getProxy();
       proxy.delete();


    }
}
```

上面客户类其实还调用了几个之前用的类：其实放在一个包下也可以，这里就是直接引用了

```java
package com.wang.demo02;

public interface UserService {
    public void add();
    public void delete();
    public void  update();
    public void query();
}
```

```java
package com.wang.demo02;

public class UserServiceImpl implements UserService {


    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {

        System.out.println("删除了一个用户");
    }

    public void update() {

        System.out.println("修改了一个用户");
    }

    public void query() {

        System.out.println("查询了一个用户");
    }
}
```

最后执行Client的输出测试，修改增删改查只在最后一句proxy.delete();修改即可

**总结：**

- Proxy是用来生成动态代理实例的
- InvocationHandler则是调用处理程序，返回结果

proxy和InvocationHandler是动态代理的两个核心

动态代理的好处：

- 可以使真实角色的操作更加纯粹，无需去关注一些公共的业务；
- 公共业务就交给代理角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理；
- 一个动态代理类代理的是一个接口，一般就是对应的一类业务
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可；

比如增加一个接口实现类UserServiceImpl2，那么只要他实现了接口UserService并

在Client类中对应修改了实现类的名字就好



PS:框架底层和反射有关，反射需要熟悉一下

#### 了解AOP

aop：面向切面编程

用动态代理其实是可以做到切面编程的，下面是如何用spring去实现aop

- 横切关注点：要横切加入的方法或功能比如说日志，安全，缓存，事务
- 切面（Advice）比如log日志类
- 通知：类中的一个方法
- 目标Target：就是一个被通知对象
- 代理proxy：字面意思就是生成的代理类
- 切入点：
- 连接点 ：

使用Spring实现AOP：

需要导入一个包

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>

```

两个包service和log

service就是借口和增删改查的实现类

log

```java
package com.wang.log;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class log implements MethodBeforeAdvice {
    //method：要执行的目标对象的方法
    //args:参数
    //target：目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");

    }
}

```

AfterLog:

```java
package com.wang.log;

import org.springframework.aop.AfterAdvice;
import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {
    //returnValue 返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法,"+"返回结果为："+returnValue);
    }
}

```

xml配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

<!--注册bean -->
    <bean id="UserService" class="com.wang.service.UserServiceImpl"/>
    <bean id="log" class="com.wang.log.log"/>
    <bean id="afterLog" class="com.wang.log.AfterLog"/>

   <!--方式一，使用Spring API接口 -->
<!--配置aop ,需要导入aop约束，比如上面的一部分链接-->
    <aop:config>
        <!--切入点，expression表达式。execution（要执行的位置）,有五个参数 -->
        <!-- ..表示可以有任意个参数-->
        <aop:pointcut id="pointcut" expression="execution(* com.wang.service.UserServiceImpl.*(..) )"/>

        <!-- 执行环绕增加，advisor-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>

        
    </aop:config>



    <!-- more bean definitions go here -->

</beans>
```

测试类：

```java
package com.wang.log;

import org.springframework.aop.AfterAdvice;
import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {
    //returnValue 返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法,"+"返回结果为："+returnValue);
    }
}
```



#### AOP实现方法二

ps：xml文件中可以选择要多行注释的代码段然后ctrl+shift+/



关于execution表达式：

expression="execution(* com.wang.service.UserServiceImpl.*(..))"/>

第一个星号表示要返回的类型为任意，第二个表示要拦截的包名，

加了星号为所有方法，两个点点表示任意参数

关注点就是我们要插入的功能或者 方法，比如日志等

关注点被模块化后成为一个类，这就是切面；

至于目标Target和代理proxy都是Spring代为执行创建（invocationHandler）

```java
package com.wang.diy;

public class DiyPointCut {

    public void before(){
        System.out.println("---方法执行前---");
    }
    public void after(){
        System.out.println("---方法执行后---");
    }

}
```

xml

```xml
    <!--aop方式二 -->

<bean id="diy" class="com.wang.diy.DiyPointCut"/>
    <aop:config>

        <!--自定义切面，ref：要引用的类 -->
        <aop:aspect ref="diy">
            <!--切入点 -->
            <aop:pointcut id="point" expression="execution(* com.wang.service.UserServiceImpl.*(..))"/>
            <!-- 通知，定义在point处执行before方法和after方法-->
            <aop:before method="before" pointcut-ref="point"/>

            <aop:after method="after" pointcut-ref="point"/>

        </aop:aspect>
    </aop:config>

```

#### 注解实现AOP（算是第三种吧

AnnotationPointCut类

```java
package com.wang.diy;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

//使用注解实现AOP
@Aspect//标注这个类是一个切面
public class AnnotationPointCut {

    @Before("execution(* com.wang.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("==方法执行前==");
    }
    @After("execution(* com.wang.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("==方法执行后==");
    }
    //在环绕增强中我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.wang.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        Signature signature = jp.getSignature();//获得签名（也就是这个类的信息
        System.out.println("sinature："+signature);

        //执行方法
        Object proceed = jp.proceed();
        System.out.println("环绕后");
        System.out.println(proceed);

    }


}
```

xml中需要这些语句：

```xml
<!--注册bean -->
    <bean id="UserService" class="com.wang.service.UserServiceImpl"/>
    <bean id="log" class="com.wang.log.log"/>
    <bean id="afterLog" class="com.wang.log.AfterLog"/>

   <!-- 方式三 注解实现aop-->
<bean id="annotationPointCut" class="com.wang.diy.AnnotationPointCut"/>
    <!--开启注解支持 -->
<aop:aspectj-autoproxy/>
```

测试类和之前的例子中的一样不变

```java
import com.wang.service.UserService;
import com.wang.service.UserServiceImpl;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {



    public static void main(String[] args) {
        //CPX
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       //动态代理的是接口，所以必须是接口UserService
        UserService userService = context.getBean("UserService", UserService.class);
        //UserServiceImpl.class添加这个语句创建对象时就不用强制转换了
        userService.add();

    }
}
```

相关类就是service包下的一个借口和一个实现类

通过环绕和before，after方法理解切面思想



#### 整合Mybatis：

1.导入相关jar包

- junit
- mybatis
- mysql数据库
- spring相关
- aop置入
- mybatis-spring [new]

2.编写配置文件

3.测试



需要导入一些包/依赖pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>spring-study</artifactId>
        <groupId>com.wang</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>spring-10-mybatis</artifactId>
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.21</version>
    </dependency>
    <!--mybatis-->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.6</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.13</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.2</version>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.16.10</version>
    </dependency>





</dependencies>

<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>

                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>

    </resources>
</build>

</project>





</dependencies>

</project>
```

新建实体类User

```java
package com.wang.pojo;

import lombok.Data;

@Data
public class User {
    private int id;
    private String name;
    private String pwd;
}
```



编写mybatis-config.xml如下

顺便拷贝一下mybatis-config的配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    
    <typeAliases>
        <package name="com.wang.pojo"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper class="com.wang.mapper.UserMapper"/>
    </mappers>

</configuration>
```

编写mapper下借口UserMapper和对应xml文件

然后注册接口<mapper></mapper>

记得考虑maven静态资源问题：

要在本模块pom.xml文件中加入

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>

                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>

    </resources>
</build>
```



测试类

```java
import com.wang.mapper.UserMapper;
import com.wang.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.apache.ibatis.io.Resources;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;


public class MyTest {
    @Test
    public void test01() throws IOException {
        String resource="mybatis-config.xml";
        InputStream in = Resources.getResourceAsStream(resource);

        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.selectUser();
        //userList.for
        for (User user : userList) {
            System.out.println(user);
            
        }



    }
}
```

注意：Mybatis，Spring这两者版本和Mybatis-Spring版本有关系，

3.5+Mybatis和Spring5.0+需要Mybatis-Spring2.0版本

#### 整合mybatis方式一：



1.编写数据源配置

也就是spring-dao中的

```
<!--DataSource:使用Spring的数据源替换Mybaties的配置   c3p0,dbcp , druid-->

<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true"/>
    <property name="username" value="root"/>
    <property name="password" value="password"/>
</bean>
```

2.sqlSessionFactury

```
<!-- sqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <!--绑定mybatis配置文件 -->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <property name="mapperLocations" value="classpath:com/wang/mapper/*.xml"/>
</bean>
```

3.sqlSessionTemplate

```
<!--sqlSessionTemplate模板：就是我们使用的sqlSession -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--sqlSessionTemplate只能使用构造器注入,因为它没有set方法，自然无法set注入 -->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
        <!-- 当构造函数有多个参数时，可以使用constructor-arg标签的index属性，index属性的值从0开始-->

    </bean>
```

4.需要给接口加实现类[]

UserMapper.xml配置

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.wang.mapper.UserMapper">
<select id="selectUser" resultType="user">
    select * from mybatis.user;
</select>


</mapper>
```

实现类

```
package com.wang.mapper;

import com.wang.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserMapperImpl implements UserMapper{

    //原本，我们的所有操作都是要sqlSession来执行，现在，我们使用sqlSessionTemplate
    private SqlSessionTemplate sqlSession;

    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }

    public List<User> selectUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

5.将自己写的实现类注入到Spring中

```
</bean>

<bean id="userMapper" class="com.wang.mapper.UserMapperImpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

6.测试使用

具体例子中见子项目spring-10-mybatis



#### mybatis与spring整合第二种方法

利用SqlSessionDaoSupport类实现

通过继承类来实现整合：

```java
public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper{
    public List<User> selectUser(){
        //SqlSession sqlsession = getSqlSession();
        //UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        //官方建议整合成一句
        return getSqlSession().getMapper(UserMapper.class).selectUser();
    } 
}
```

然后在ApplicationContext.xml中配置spring

```xml
<bean id="UserMapper2" class="com.wang.mapper.UserMapperImpl2">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```



```java
@Test
public void test01() throws IOException {

    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("ApplicationContext.xml");
    UserMapper userMapper = context.getBean("userMapper2", UserMapper.class);
    for (User user : userMapper.selectUser()) {
        System.out.println(user);
    }



}
```



#### 事务：

把一组事务当成一个业务来做，要么都成功，要么都失败（一组事务中）

在项目开发中事务很重要，涉及到数据的一致性问题

确保完整性和一致性



事物的ACID原则：

- A：原子性

- C:一致性

- I:隔离性：多个业务可能操作同一个资源，防止数据损坏

- D:持久性：事务一旦提交，无论系统发生什么问题，结果都不会再被影响

  被持久化的写到存储器中

  transaction：事务

使用mybatis=spring后他可以借助Spring中的DataSourceTransactionManager

来实现事务管理

#### Spring声明式事务

声明式事务：AOP

编程式事务：需要在代码中进行事务的管理

当然交给容器管理事务就属于声明式事务；

见spring-11-transaction



总结：



















未完成任务：
	java 多线程
	java集合



​	Springboot
​	SpringCloud

​	SpringMVC	（包含在spring中

