### 学习SpringMVC      ——02.01-



#### 回顾MVC：

模型（dao,service），视图(jsp)，控制器(servlet)



以往的mvc开发流程：

设计dao，连接数据库，

service业务，

servlet 来接受前端数据，转发，重定向

jsp/html 





ps：vo:本质是删选，细分后的实体类（除去一些不必要的



model：数据模型，提供要展示的数据，一般会分为数据Dao和行为Service两部分；

view：负责模型的展示，一般就是用户界面，客户所见到的东西

controller：接受用户请求，委托给模型进行处理（状态改变），处理后把返回的模型数据返回给

视图，由视图层负责展示，可以把控制器理解为调度员；



最典型的MVC就是JSP+servlet+javabean的模式



JSP：本质就是一个servlet

你的项目的架构，是设计好的，还是演进的？

必然是演进，因为架构是一步步演化的





- 用户发请求；

- servlet接受请求数据，并调用对应的业务逻辑方法
- 业务处理完毕，返回更新后的数据给servlet
- servlet转向到jsp，由jsp来渲染页面
- 响应给前端更新后的页面

职责分析：

controller：

取得表单数据，

调用业务逻辑，

转向指定的页面



model：（dao+service）

业务逻辑，

保存数据的状态





view：

显示页面



##### 回顾servlet

新建一个maven工程，其中子工程叫springMVC-01-servlet

子项目右键添加框架支持（framework support)，勾选web

添加依赖

```xml
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

    </dependencies>
```

编写一个servlet类，用来处理用户请求，getParamter之类的

然后调用业务层（这里未使用

视图转发或者重定向，比如getRequestDispatcher等

还有web.xml文件中定义servlet

还有jsp页面用来表单提交，以及对应的页面来输出

and so on..



MVC框架需要做什么：

将url映射到java类或java类的方法，

封装用户提交的数据，

处理请求——调用相关的业务处理——封装响应数据

将响应的数据进行渲染	.jsp/html等表示层面的数据；



常见服务器端MVC框架有：structs，springmvc，asp.net mvc

常见的前端MVC框架：vue，react，backbone等等

由MVC又演化出一些模式：MVP，MVVM等等



#### 初识SpringMVC

springMVC是spring framework的一部分，是基于java实现的轻量级web框架；

约定优于配置（同maven

支持RESTful（非？传参），数据验证，格式化，本地化，主题等



围绕DispacherServlet  [调度servlet ]设计



SpringMVC本质上围绕一个Httpservlet



**具体实现流程：**

1.在web.xml中配置DispatcherServlet：

```xml
<!--注册 DispatcherServlet -->
<servlet>
	<servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--关联一个springmvc的配置文件 [servlet-name]-servlet.xml -->
    <init-param>
    	<param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc-servlet.xml</param-value>
    </init-param> 
    <!--启动级别-1 -->
   <load-on-startup>1</load-on-startup>
</servlet>

<!--匹配所有的请求：不包括.jsp -->
<!--匹配所有的请求：包括.jsp -->
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

关于 /和  /*:

/ :只匹配所有的请求，不会去匹配jsp页面

/*：匹配所有的请求，包括jsp 

使用/*会无限访问拼接好的jsp页面然后死循环。。



2.DispatcherServlet要绑定springmvc的配置文件

```xml
<!--DS要绑定一个springmvc的配置文件 [servlet-name]-servlet.xml -->
<init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc-servlet.xml</param-value>
</init-param>
```



3.然后去编写配置文件springmvc-servlet.xml（规范：在resource下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

添加处理映射器

`<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>`

添加处理器适配器

`<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>`

ps：映射器和适配器后续会改用注解实现~

然后加入视图解析器

```
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
</bean>
```

4.在包下新建HelloController类

```java
package com.wang.controller;


import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

//注意：这里我们先导入Controller接口
    public class HelloController implements Controller {

        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            //ModelAndView 模型和视图
            ModelAndView mv = new ModelAndView();

            //书写业务代码
            String result = "HelloSpringMVC";
            //封装对象，放在ModelAndView中
            mv.addObject("msg",result);//键值对
            //视图跳转
            mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp

            return mv;
        }

    }
```

因为上面的处理器适配器需要根据bean的id来执行，所以还需要添加一条语句

5.因为上面的处理器适配器需要根据bean的id来执行，所以还需要添加一条语句

自己的类交给SpringIOC容器，注册bean

```xml
<!--Handler-->
<bean id="/hello" class="com.wang.controller.HelloController"/>
```

6.书写要跳转的页面还有要显示的信息：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
<title>wang</title>
</head>
<body>
${msg}
</body>
</html>
```

然后运行tomcat，注意更改要运行的对象：

Tomcat的设置中点击Deployment，通过右侧的+，—号来添加删除要运行的项目服务；

访问404时看看路径是否错误，在这里是另外一个问题：需要在project structure中Artfacts添加lib目录然后加入各种应该加入的包即可。



##### SpringMVC程序执行原理



![img](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IicxBZbkh0D4dJJiaXSzGEXyzsXDPy7oAJFsBvvBibiaFWpSp75vFIEOCBm7wnt4JKXJCHB9MflUycKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

ps：下面编号与上面不是每二个都一一对应的

1.用户发起请求，通过DispatcherServlet请求位于服务器localhost8080上的hello控制器

2.根据url请求查找处理器映射器handler

3.获得对应的处理器映射器后，找对应的适配器HandlerAdapter，

4.适配器按照特定的规则执行Handler，（适配到的处理器可以理解为控制器

5.handler让具体的Controller执行，

6.controller会去调用业务层，然后返回，

7.controller返回时 携带数据+要跳转的视图（可以理解为返回ModelAndView）

8.DispatcherServlet调用视图解析器（ViewResolver）来解析HandlerAdapter

传递的逻辑视图名；

9.然后解析器将解析的逻辑视图名传给DispatcherServlet

10.DS根据视图解析器解析结果，调用具体的视图

11.最终视图呈现给用户



上述原理步骤可以理解为三部分：

- 适配请求
- controller执行具体请求
- 视图解析，并返回结果



##### 打包（Artifacts）

打包，就是针对编译后的java类，web资源的整合，每个web类型的module都应该部署好这个操作；

具体页面在Project Structure->Artifacts中



#### 注解开发springMVC

首选，因为maven过滤问题，建议在当前模块下pom.xml中加入如下语句

```xml

<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

在project structure->Artifacts中加入lib和依赖包

然后在web.xml中加入固定的注册语句

```xml
<!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

```

配置文件springmvc-servlet，如下语句是注解开发的固定套路

```xml
    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.wang.controller"/>
    <!-- 让Spring MVC不处理静态资源 css，mp3,js,heml,mp4等等-->
    <mvc:default-servlet-handler />
    <mvc:annotation-driven />
    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
        <!--
        支持mvc注解驱动
            在spring中一般采用@RequestMapping注解来完成映射关系
            要想使@RequestMapping注解生效
            必须向上下文中注册DefaultAnnotationHandlerMapping
            和一个AnnotationMethodHandlerAdapter实例
            这两个实例分别在类级别和方法级别处理。
            而annotation-driven配置帮助我们自动完成上述两个实例的注入。
         -->
```

@Controller注解，免去声明bean的那条语句



然后写一个函数来封装数据并返回mv给视图解析器处理

```java
@RequestMapping("/h1")    
public String hello(Model model){

        //封装数据，利用model
        model.addAttribute("msg","hello,springmvc");
        return "hello";//这个用于给视图解析器处理
    }
```

然后添加url请求，也可以用注解实现，@RequestMapping("/hello")，

然后在要请求的页面输出数据：${msg}

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

${msg}
    
</body>
</html>

```

运行后url访问h1显示hello，springmvc



#### 关于controller配置

需要配置上下文的包路径：

<context:componment-scan base-package="com.wang.controller">

过滤静态资源以及自动注入：

    <mvc:default-servlet-handler />
    <mvc:annotation-driven />
还需要配置视图解析器

controlle相关类推荐使用注解@Controller实现，可以使用多个函数

函数内封装mv（modelandview）并return"xxx"给视图解析器作为拼接，去请求xxx.jsp页面

还需在函数前加注解@RequestMapping("/t2")  ：映射地址   



如果@RequestMapping也添加在了类的上面，那么路径由类上的注解和函数上面的注解组成，

这是一个父子关系（上下关系；



#### RestFul风格

用斜线/风格代替问号?传参，是一个资源定位和资源操作的风格；



和传统方式操作资源相比，restful基本只是url后面的方法（请求方式）不一样；



http://127.0.0.1/item/xxx.get

原来的方法：

```
@RequestMapping("/add)
public String test( int a,int b, Model model){

    int res = a+b;
    model.addAttribute("msg","结果1为"+res);

    return "hello";
}
```

请求：http://localhost:8080/add?a=1&b=2

新的方法：

```
@RequestMapping(value="/add/{a}/{b}",method=RequestMethod.GET)
public String test( @PathVariable int a, @PathVariable int b, Model model){

    int res = a+b;
    model.addAttribute("msg","结果1为"+res);

    return "hello";
}
```

传参：http://localhost:8080/add/1/3 意思是传递1和3，页面会显示1+3的结果，

**报错HTTP500一般是代码问题，HTTP404可能是请求问题，405是不支持**



如果报错，修改代码后可以run Tomcat的时候选择更新资源文件；

顺便一提任何类型和字符串类型相加都会向上转型为String类型；

RestFul具有简洁，高效，安全的特点

高效是可缓存；



@小黄鸭调试法@ hh





#### 数据处理及跳转（重定向和转发）

重定向不经过视图解析器，so，

事实上如果不使用视图解析器的话需要在controller类中的函数里return "全路径"

当然不建议这样写，

重定向的话	return "redirect:/index.jsp";



关于转发和重定向区别：

转发只请求一次，而且页面跳转后地址栏不变，仍显示上一个页面的地址；

重定向 请求次数为2次，而且会显示新的地址，请求域中的数据会丢失，因为是2次请求

ps：如果要保留请求域中的数据，使用转发，否则使用重定向。

以后访问数据库，增删改使用重定向，查询使用转发。

而且，重定向无法访问WEB-INF下的内容，而且不经过视图解析器



#### 接受请求参数和数据回显



url传递参数时参数名需要一致，也就是必须传入name=xxx才行,  除非加上注解@RequestParam("xxx")

当然，建议一不一致都要把这个注解加上

```java
package com.wang.controller;


import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/t1")
    public String test1(@RequestParam("username") String name, Model model){
        //接收前端参数name

        System.out.println("接受前端参数为"+name);
        //将结果返回给前端
        model.addAttribute("msg",name);


        //视图跳转
        return "test";
    }

}
```

然后请求：http://localhost:8080/user/t1?username=wang 页面显示wang



上面是前端传递为单一参数，若传递的是对象：要调用id，name，age需如下：

```java
@GetMapping("/t2")
public String test2(User user){
    //接收前端参数name

    System.out.println(user);


    //视图跳转
    return "test";
}
```

前端传递参数：http://localhost:8080/user?name=wang&id=1&age=15

不一致则会返回null



##### 数据显示到前端

一种是ModelAndView，是开头介绍的，

第二种就是注解，（

第三种ModelMap

第四种Model：

```java
@RequestMapping("/ct2/hello")
public String hello(@RequestParam("username") String name, Model model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("msg",name);
   System.out.println(name);
   return "test";
}
```



#### 解决乱码

一般利用过滤器filter解决传递层面造成的乱码

注意，实现Filter时这个Filter是javax.servlet.*包下的；“

不过这种方法只能改善get方法的乱码....所以不贴代码了；

完善后的代码如下：

控制部分：

```java
@Controller
public class EncodingaController {


        @RequestMapping("/ttk")
        public String test1(String name, Model model){
            //接收前端参数name

            //将结果返回给前端
            model.addAttribute("msg",name);


            //视图跳转
            return "test";
        }
}
```

xml部分：

```
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

然后运行tomcat（如果更新过xml一定要restart，然后输入参数http://localhost:8080/form.jsp

然后输入文本即可



**在这里一定要注意：**

**/ :只匹配所有的请求，不会去匹配jsp页面**

**/*：匹配所有的请求，包括jsp** 

这里因为过滤器要去涉及jsp，所以需要加*



顺便一提：tomcat的services输出如果出现乱码：

在 tomcat / conf 目录下，设置 logging.properties ，增加参数 java.util.logging.ConsoleHandler.encoding = GBK即可



#### JSON

前后端分离时代，

后端部署后端，提供接口；



json



前端独立部署，负责渲染后端的数据；



json（js对象标记）：采用独立于编程语言的文本格式来存储和表示数据；

用" "包裹，分号;分割

script标签只能存在于静/动态页面

```
<script type="text/javascript">
    //编写一个javascript对象
    var user = {
        name:"皮蛋",
        age:16,
        sex:"男"
    };

    //将js对象转化为json对象
    var json = JSON.stringify(user);

    console.log(json);

    console.log("我是分割线")

    //可以将json对象转化为js对象

    var obj = JSON.parse(json);
    console.log(obj);


</script>
```

js放在script标签内，将后台准备好的json对象传递给前端，前端用此方法可以获得js对象

二者可以互相转化；

二者的关系：JSON是JS（JavaScript）对象的字符串表示，它使用文本表示一个JS对象的信息，本质是一个字符串

所以重点是java如何生成JSON对象，详见下一段



#### jackson的使用

代码见springMVC-04-json

jackson目前是一个比较好的json解析工具，除此外还有fastjson等等

需要在pom.xml中导入jar包：（这个是当前最新的

```
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.10.0</version>
</dependency>
```

然后web.xml中配置servlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">



    <!--1.注册servlet-->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!-- 启动顺序，数字越小，启动越早 -->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--所有请求都会被springmvc拦截 -->
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/</url-pattern>
    </filter-mapping>
</web-app>
```

然后resources下编写springmvc-servlet配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
    <context:component-scan base-package="com.wang.controller"/>

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

新建实体类并加入lombok依赖（偷懒用，省去写setget，tostring这些方法



json乱码统一解决,此代码加在spring-servlet配置文件中

```xml
<!--json乱码统一解决 -->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

另外如果使用了lombok的注解还报错，但是又不影响运行的话可能是lombok创建了函数但是ide不知道hhh

想取消报错可以点击setting，安装对应插件（plugin），在其中搜索lombok，同时设置 Setting -> Compiler -> Annotation Processors -> Enable annotation processing勾选。



在类上直接使用 **@RestController** ，这样子，里面所有的方法都只会返回 json 字符串了，不用再每一个都添加@ResponseBody ！我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！



输出集合：

```java
@RequestMapping("/json2")
public String json2() throws JsonProcessingException {

    //创建一个jackson的对象映射器，用来解析数据
    ObjectMapper mapper = new ObjectMapper();
    //创建一个对象
    User user1 = new User("蒙面1号", 3, "男");
    User user2 = new User("蒙面2号", 3, "男");
    User user3 = new User("蒙面3号", 3, "男");
    User user4 = new User("蒙面4号", 3, "男");
    List<User> list = new ArrayList<User>();
    list.add(user1);
    list.add(user2);
    list.add(user3);
    list.add(user4);

    //将我们的对象解析成为json格式
    String str = mapper.writeValueAsString(list);
    return str;
}
```





输出时间：

```java
@RequestMapping("/json4")
public String json4() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //不使用时间戳的方式
   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
   //自定义日期格式对象
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   //指定日期格式
   mapper.setDateFormat(sdf);

   Date date = new Date();
   String str = mapper.writeValueAsString(date);

   return str;
}
```

为了省事可以新建一个工具类：

```java
package com.kuang.utils;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

import java.text.SimpleDateFormat;

public class JsonUtils {
   
   public static String getJson(Object object) {
       return getJson(object,"yyyy-MM-dd HH:mm:ss");
  }

   public static String getJson(Object object,String dateFormat) {
       ObjectMapper mapper = new ObjectMapper();
       //不使用时间差的方式
       mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
       //自定义日期格式对象
       SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
       //指定日期格式
       mapper.setDateFormat(sdf);
       try {
           return mapper.writeValueAsString(object);
      } catch (JsonProcessingException e) {
           e.printStackTrace();
      }
       return null;
  }
}
```

举例

```java
@RequestMapping("/json5")
public String json5() throws JsonProcessingException {
   Date date = new Date();
   String json = JsonUtils.getJson(date);
   return json;
}
```

同理，可以把输出集合的函数改一下：

```java
@RequestMapping("/json2")
public String json2() throws JsonProcessingException {
 
    //创建一个对象
    List<User> list = new ArrayList<User>();
    User user1 = new User("蒙面1号", 3, "男");
    User user2 = new User("蒙面2号", 3, "男");
    User user3 = new User("蒙面3号", 3, "男");
    User user4 = new User("蒙面4号", 3, "男");

    list.add(user1);
    list.add(user2);
    list.add(user3);
    list.add(user4);

    //将我们的对象解析成为json格式
 
    return JsonUtils.getJson(list);
}
```



#### 另外一种json工具 FastJson

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的

需要导入依赖：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.60</version>
</dependency>
```



然后可以在函数里数显了，如下，多种情况：

```java
@RequestMapping("/json6")
public  String json6(){

    //创建一个对象
    List<User> list = new ArrayList<User>();
    User user1 = new User("蒙面1号", 3, "男");
    User user2 = new User("蒙面2号", 3, "男");
    User user3 = new User("蒙面3号", 3, "男");
    User user4 = new User("蒙面4号", 3, "男");

    list.add(user1);
    list.add(user2);
    list.add(user3);
    list.add(user4);

    System.out.println("*******Java对象 转 JSON字符串*******");
    String str1 = JSON.toJSONString(list);
    System.out.println("JSON.toJSONString(list)==>"+str1);
    String str2 = JSON.toJSONString(user1);
    System.out.println("JSON.toJSONString(user1)==>"+str2);

    System.out.println("\n****** JSON字符串 转 Java对象*******");
    User jp_user1=JSON.parseObject(str2,User.class);
    System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

    System.out.println("\n****** Java对象 转 JSON对象 ******");
    JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
    System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

    System.out.println("\n****** JSON对象 转 Java对象 ******");
    User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
    System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);

    return str1;
}
```



#### SSM整合：

环境：IDEA2019+MySQL8.0+Tomcat 9 +Maven3.6



项目设计思路：

需求分析->设计数据库->业务->前端界面

先创建数据库:ssmbuild

以及books表

```sql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

DROP TABLE IF EXISTS `books`;

CREATE TABLE `books` (
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARCHAR(200) NOT NULL COMMENT '描述',
KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从入门入土');
```



需要的依赖：

```xml
<dependencies>
   <!--Junit-->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
   </dependency>
   <!--数据库驱动-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.21</version>
   </dependency>
   <!-- 数据库连接池 -->
   <dependency>
       <groupId>com.mchange</groupId>
       <artifactId>c3p0</artifactId>
       <version>0.9.5.2</version>
   </dependency>

   <!--Servlet - JSP -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>servlet-api</artifactId>
       <version>2.5</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet.jsp</groupId>
       <artifactId>jsp-api</artifactId>
       <version>2.2</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jstl</artifactId>
       <version>1.2</version>
   </dependency>

   <!--Mybatis-->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.2</version>
   </dependency>
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis-spring</artifactId>
       <version>2.0.2</version>
   </dependency>

   <!--Spring-->
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
    <!--lombok -->
            <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.10</version>
        </dependency>
</dependencies>
```

还有静态资源导出问题：

```xml
<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

新建包（路径）：com.wang.pojo,

service,

dao

controller

以及filter，utils等等

以及各自的核心配置文件：mybatis-config,applicationContext

在resources下 初始配置文件：mybatis-config:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

applicationContext:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

目前的基本要素都有了，开始编写代码

资源下新建：database.properties:

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver

jdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=password
```

mybatis-config中 经常会备注 typeAliases：起别名

```xml
<!--取别名 -->
<typeAliases>
    <package name="com.wang.pojo"/>
</typeAliases>
```

pojo下创建实体类books

```java
package com.wang.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Books {

    private int bookID;
    private String bookName;
    private int bookCounts;
    private String detail;
}
```



dao层下写接口：负责定义一些抽象方法

```java
package com.wang.dao;

import com.wang.pojo.Books;

import java.util.List;

public interface BookMapper {
    //增加一个Book
    int addBook(Books book);

    //根据id删除一个Book
    int deleteBookById(int id);

    //更新Book
    int updateBook(Books books);

    //根据id查询,返回一个Book
    Books queryBookById(int id);

    //查询全部Book,返回list集合
    List<Books> queryAllBook();
}
```

然后新建接口对应的mapper配置文件:BookMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.wang.dao.BookMapper">

   <!--增加一个Book-->
   <insert id="addBook" parameterType="Books">
      insert into ssmbuild.books(bookName,bookCounts,detail)
      values (#{bookName}, #{bookCounts}, #{detail})
   </insert>

   <!--根据id删除一个Book-->
   <delete id="deleteBookById" parameterType="int">
      delete from ssmbuild.books where bookID=#{bookID}
   </delete>

   <!--更新Book-->
   <update id="updateBook" parameterType="Books">
      update ssmbuild.books
      set bookName = #{bookName},bookCounts = #{bookCounts},detail = #{detail}
      where bookID = #{bookID}
   </update>

   <!--根据id查询,返回一个Book-->
   <select id="queryBookById" resultType="Books">
      select * from ssmbuild.books
      where bookID = #{bookID}
   </select>

   <!--查询全部Book-->
   <select id="queryAllBook" resultType="Books">
      SELECT * from ssmbuild.books
   </select>

</mapper>
```

注意：@Param("bookID")可以方便给sql语句传入参数

当你使用了使用@Param注解来声明参数时，如果使用 #{} 或 ${} 的方式都可以,当你不使用@Param注解来声明参数时，必须使用使用 #{}方式。如果使用 ${} 的方式，会报错

然后将mapper绑定到mybatis-config中

```
<mappers>
    <mapper resource="com/wang/dao/BookMapper.xml"/>
</mappers>
```

至此，dao层差不多就搞定了，主要就是定义一些实体类和sql语句



然后就是service层，和数据库对应的就是增删改查这些，和dao层没有太大区别

创建BookService接口，同样定义增删改查的抽象方法

```java
package com.wang.service;

import com.wang.pojo.Books;

import java.util.List;


//BookService:底下需要去实现,调用dao层
public interface BookService {
    //增加一个Book
    int addBook(Books book);
    //根据id删除一个Book
    int deleteBookById(int id);
    //更新Book
    int updateBook(Books books);
    //根据id查询,返回一个Book
    Books queryBookById(int id);
    //查询全部Book,返回list集合
    List<Books> queryAllBook();
}
```

然后写对应的实现类，一般都是service层调用dao层

```java
package com.wang.service;

import com.wang.dao.BookMapper;
import com.wang.pojo.Books;
import java.util.List;

public class BookServiceImpl implements BookService {

    //service层调用dao层的操作，设置一个set接口，方便Spring管理
    private BookMapper bookMapper;

    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }

    public int addBook(Books book) {
        return bookMapper.addBook(book);//比如这个就是业务层在调用dao的addBook
    }

    public int deleteBookById(int id) {
        return bookMapper.deleteBookById(id);
    }

    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }

    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }

    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }
}
```

##### 然后整合spring层：

建立spring-dao.xml

记得把spring-dao和applicationContext放在一起

然后文件中需要：

- 关联数据库配置文件

- 连接池（有很多种，dbcp，c3p0，druid（英[ˈdruːɪd]）等等

         dbcp 半自动化操作 不能自动连接
          c3p0 自动化操作（自动的加载配置文件 并且设置到对象里面）
          druid和c3p0用的比较多；

- sqlSessionFactory

等，

这里连接池用c3p0

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置整合mybatis -->
    <!-- 1.关联数据库文件 -->
    <context:property-placeholder location="classpath:database.properties"/>

    <!-- 2.数据库连接池 -->
    <!--数据库连接池
        dbcp 半自动化操作 不能自动连接
        c3p0 自动化操作（自动的加载配置文件 并且设置到对象里面）
    -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 配置连接池属性 -->
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

    <!-- 3.配置SqlSessionFactory对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

    <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
    <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.wang.dao"/>
    </bean>

</beans>
```

然后开始配置spring-service.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 扫描service相关的bean -->
    <context:component-scan base-package="com.wang.service" />

    <!--BookServiceImpl注入到IOC容器中-->
    <bean id="BookServiceImpl" class="com.wang.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource" />
    </bean>

</beans>
```

另外为了有效引入，需要在applicationContext.xml中加入其它两个配置文件

```xml
<import resource="classpath:spring-dao.xml"/>
<import resource="classpath:spring-service.xml"/>
```

其中spring-service.xml中是利用配置法实现，

```xml
    <!--BookServiceImpl注入到IOC容器中-->
    <bean id="BookServiceImpl" class="com.wang.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>
```

如果用注解实现自动注入：要在接口实现类中加入两个注解@Service和@Autowired

```java
package com.wang.service;

import com.wang.dao.BookMapper;
import com.wang.pojo.Books;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
@Service
public class BookServiceImpl implements BookService {

    //service层调用dao层的操作，设置一个set接口，方便Spring管理
    @Autowired//可以自动装配，省略setget方法，然后跳至BookMapper接口
    private BookMapper bookMapper;

    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }

    public int addBook(Books book) {
        return bookMapper.addBook(book);//比如这个就是业务层在调用dao的addBook
    }

    public int deleteBookById(int id) {
        return bookMapper.deleteBookById(id);
    }

    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }

    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }

    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }
}
```

@Autowired 注释，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。 通过 @Autowired的使用来消除 set ，get方法。



##### 最后整合springMVC层



想要变成一个web项目，需要添加web支持：项目右键添加（add frameworksupport），然后勾选Web Application 

然后配置web.xml:

包括加载dispatcherServlet，然后添加乱码过滤，注意是/*，只有/是无法拦截页面的乱码的，然后设置session过期时间防止无响应

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">


    <!--DispatcherServlet-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <!--一定要注意:我们这里加载的是总的配置文件，之前被这里坑了！-->
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--encodingFilter-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--Session过期时间-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>


</web-app>
```

然后SpringMVC还需要在对应配置文件(spring-mvc.xml)中添加注解驱动，静态资源过滤

扫描包：controller，视图解析器等等：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/mvc
   https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 配置SpringMVC -->
    <!-- 1.开启SpringMVC注解驱动 -->
    <mvc:annotation-driven />
    <!-- 2.静态资源默认servlet配置-->
    <mvc:default-servlet-handler/>

    <!-- 3.配置jsp 显示ViewResolver视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>

    <!-- 4.扫描web相关的bean -->
    <context:component-scan base-package="com.wang.controller" />

</beans>
```



然后再把spring-mvc添加到applicationContext中，

这样的话框架的配置整合完毕，剩下的就是具体的业务代码等等



#### 添加controller内部功能和jsp实现：

查询书籍：

```java
@Controller
@RequestMapping("/book")
public class BookController {

    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    @RequestMapping("/allBook")
    public String list(Model model) {
        List<Books> list = bookService.queryAllBook();
        model.addAttribute("list", list);
        return "allBook";
    }


}
```

然后编写allbook.jsp页面

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍列表(展示书籍)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container">

    <div class="row clearfix"><!-- 清除浮动-->
        <div class="col-md-12 column"><!--在行中把列等分12份,中等大小 -->
            <div class="page-header">
                <h1>
                    <small>书籍列表 —— 显示所有书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-4 column">
            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增<书籍/a>
        </div>
    </div>

    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped"><!--hover：隔行变色 -->
                <thead>                         <!-- 表头-->
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名字</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                    <th>操作</th>
                </tr>
                </thead>

                <tbody>
                            <!--foreach遍历 -->
                <c:forEach var="book" items="${requestScope.get('list')}">
                    <tr>
                        <td>${book.getBookID()}</td>
                        <td>${book.getBookName()}</td>
                        <td>${book.getBookCounts()}</td>
                        <td>${book.getDetail()}</td>
                        <td>
                            <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
                            <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
                        </td>
                    </tr>
                </c:forEach>
                </tbody>
            </table>
        </div>
    </div>
</div>
```

然后在默认首页：index.jsp中做一些添加：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>
  <title>首页</title>
  <style type="text/css">
    a {
      text-decoration: none;
      color: black;
      font-size: 18px;
    }
    h3 {
      width: 180px;
      height: 38px;
      margin: 100px auto;
      text-align: center;
      line-height: 38px;
      background: deepskyblue;
      border-radius: 4px;
    }
  </style>
</head>
<body>

<h3>
  <a href="${pageContext.request.contextPath}/book/allBook">点击进入列表页</a>
</h3>
</body>
</html>
```

注意，如果出现500异常Invalid bound statement (not found): com.wang.dao.BookMapper.queryAllBook

请注意dao层和对应mapper是否对应，dao类内的mapper namespace="xxx.xxxx"名字是否写对



添加书籍：

首先在BookController中写好行为，可以分为两部分，一个跳到添加页面，一个调用addBook方法然后重定向到展示界面

```

//跳转到添加书籍的页面
    @RequestMapping("/toAddBook")
    public String toAddPaper() {
        return "addBook";
    }

    //添加书籍
    @RequestMapping("/addBook")
    public String addPaper(Books books) {
        System.out.println(books);
        bookService.addBook(books);
        return "redirect:/book/allBook";//重定向到  @RequestMapping("/allBook")请求
    }
```

编写addBook.jsp页面

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<html>
<head>
    <title>新增书籍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">

    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>新增书籍</small>
                </h1>
            </div>
        </div>
    </div>
    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
        <div class="form-group">
            <label>书籍名称：</label>
        <input type="text" name="bookName" class="form-control" required>
        </div>
        <div class="form-group">
            <label>书籍数量：</label>
            <input type="text" name="bookCounts" class="form-control" required>
        </div>
        <div class="form-group">
            <label>书籍详情：</label>
            <input type="text" name="detail" class="form-control" required>
        </div>
        <div class="form-group">
            <input type="submit"class="form-control" value="添加">
        </div>
    </form>

</div>
```

这里面样式是引用Bootstrap的，比较方便hh

添加required字段是为了防止表单填写不完全就提交造成的一些错误是操作，添加后只有全部文本框都填写后才可提交；



完善操作页面：也就是在书籍列表展示界面的最右侧添加一系列操作：增加，删除，修改等等：

这个放在allBook.jsp中：

```jsp
<td>
    <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
    <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
</td>
```

同理：

可以实现修改和删除函数，虽然这两个和单纯的静态查询和插入还是有一点点区别，因为更改和删除的前提是已经获取数据，毕竟我们需要携带一些参数才能进行锁定然后修改或者删除对吧，

正好，把这两个超链接放在foreach循环遍历中：可以直接调用book.getBookID()来获取id

这整个table代码如下：

```jsp
<table class="table table-hover table-striped"><!--hover：隔行变色 -->
    <thead>                         <!-- 表头-->
    <tr>
        <th>书籍编号</th>
        <th>书籍名字</th>
        <th>书籍数量</th>
        <th>书籍详情</th>
        <th>操作</th>
    </tr>
    </thead>

    <tbody>
                <!--foreach遍历 -->
    <c:forEach var="book" items="${requestScope.get('list')}">
        <tr>
            <td>${book.getBookID()}</td>
            <td>${book.getBookName()}</td>
            <td>${book.getBookCounts()}</td>
            <td>${book.getDetail()}</td>
            <td>
                <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
                <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
            </td>
        </tr>
    </c:forEach>
    </tbody>
</table>
```

然后编写对应控制器：

```java
//携带数据跳转到更新页面
    @RequestMapping("/toUpdateBook")
    public String toUpdateBook(Model model, int id) {
        Books books = bookService.queryBookById(id);
        System.out.println(books);
        model.addAttribute("book",books );
        return "updateBook";
    }
//进行更新
    @RequestMapping("/updateBook")
    public String updateBook(Model model, Books book) {
        System.out.println(book);
        bookService.updateBook(book);
        Books books = bookService.queryBookById(book.getBookID());
        model.addAttribute("books", books);
        return "redirect:/book/allBook";
    }
```

然后编写对应updateBook.jsp页面

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>修改信息</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">

    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>修改信息</small>
                </h1>
            </div>
        </div>
    </div>

    <!-- value显示为toUpdateBook函数传过来的book.xxx-->

    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
        
            <!-- 前端传递隐藏域    将id也提交但是不会在前端页面上显示-->
        <input type="hidden" name="bookID" value="${book.bookID}">
        <div class="form-group">
            <label>书籍名称：</label>
            <input type="text" name="bookName" class="form-control" value="${book.bookName}" required>
        </div>
        <div class="form-group">
            <label>书籍数量：</label>
            <input type="text" name="bookCounts" class="form-control" value="${book.bookCounts}" required>
        </div>
        <div class="form-group">
            <label>书籍详情：</label>
            <input type="text" name="detail" class="form-control" value="${book.detail}"  required>
        </div>
        <div class="form-group">
            <input type="submit"class="form-control" value="修改">
        </div>
    </form>

</div>
```

注意，这里有一个坑，因为jsp页面中没有id，即便他有根据id传递过来的bookname，bookcount，detail等等，但id并没有保存，所以需要添加一个传递隐藏域：

```
<!-- 前端传递隐藏域    将id也提交但是不会在前端页面上显示-->
<input type="hidden" name="bookID" value="${book.bookID}">
```

然后点submit后就能把id和其他三个属性一起传给public String updateBook(Model model, Books book) 

然后就可以更新了



同理可以进行删除操作

```java
@RequestMapping("/del/{bookId}")
public String deleteBook(@PathVariable("bookId") int id) {
   bookService.deleteBookById(id);
   return "redirect:/book/allBook";
}
```

关于pageContext.request.contextPath

```jsp
<a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
```

pageContext.request.contextPath获得访问根目录，然后形成了一个url请求，一般会由controller层的

@RequestMapper或者@GetMapping，@PostMapping等注解接受然后执行其下的函数

删除，这个超链接点击然后获得id，传给controller中那个删除函数，实现删除



然后在已经实现增删改查的基础上，做一些优化：比如搜索书籍这个功能：

首先需要在allBook.jsp页面添加控件：

```jsp
<div class="row">
    <div class="col-md-4 column">
    <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增书籍</a>
</div>
    <!--新增一个搜索书籍功能 -->
    <div class="col-md-4 column">
            <form action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float:right">
                <input type="text" name="queryBookName" class="form-control" placeholder="请输入要查询的书籍名称" value="${book.bookName}" >
                <input type="submit" class="btn btn-primary" value="查询">
            </form>

    </div>


</div>
```

然后在controller层添加控制函数：此处在dao层还没有定义所以先空着：

```
@RequestMapping("/queryBook")
public  String queryBook(Model model,String queryBookName){


}
```



再然后，就是从底层向上：在dao层定义函数名，

```java
//搜索书籍（根据名称
Books queryBookByName(@Param("bookName") String bookName);
```

然后在bookMapper中写好sql语句：

```xml
 <!--根据bookName查询,返回一个Book-->
 <select id="queryBookByName" resultType="Books">
   select * from ssmbuild.books
   where bookName = #{bookName}
</select>
```

然后，service也要增加对应的功能，在BookService中添加;

```java
//根据书名查询
Books queryBookByName(String bookName);
```

实现类BookServiceImpl中也要添加：

```java
public Books queryBookByName(String bookName) {
    return bookMapper.queryBookByName(bookName);
}
```

然后回过头完善controller中的函数，（调用业务层等等：

```
@RequestMapping("/queryBook")
public  String queryBook(Model model,String queryBookName){
   Books books = bookService.queryBookByName(queryBookName);
    List<Books> list = new ArrayList<Books>();
    list.add(books);

    model.addAttribute("list", list);

     return "allBook";


}
```

这样初步就可以实现根据书名查询单本书籍了，然后为了能够返回查询界面，在查询书籍按钮的附进入显示页面：

```
    <div class="col-md-4 column">
    <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增书籍</a>
        <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/allBook">显示全部书籍</a>
</div>
```

这样的话即使因为查询框内返回空导致查不出书籍也可以返回原界面，

下一步，继续优化，实现当查询为空的时候直接显示原界面：（加个if判断

```
@RequestMapping("/queryBook")
public  String queryBook(Model model,String queryBookName){
   Books books = bookService.queryBookByName(queryBookName);
    List<Books> list = new ArrayList<Books>();
    list.add(books);

    if(books==null){//如果查询为空
        list = bookService.queryAllBook();//直接显示原有全部信息
        model.addAttribute("error","未查到相关信息");
    }
    model.addAttribute("list", list);

     return "allBook";


}
```

然后如果为空显示提示信息:不存在

需要在allBook中添加：`<span style="color: red; font-weight: bold">${error}</span>`

span是一种对行内元素进行组合的标签,具体位置如下：

```jsp
<!--新增一个搜索书籍功能 -->
<div class="col-md-4 column">
    <form action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float:right">
        <span style="color: red; font-weight: bold">${error}</span>
        <input type="text" name="queryBookName" class="form-control" placeholder="请输入要查询的书籍名称" value="${book.bookName}" >
        <input type="submit" class="btn btn-primary" value="查询">
    </form>

</div>
```

然后差不多就是一些界面优化了，比如借助BootStrap中的内联表单，

当`<Form class = "form-inline">`

这样，可以使得原本默认纵向排列的form格局变成横向的！

```jsp
<!--新增一个搜索书籍功能 -->
<div class="col-md-4 column">
    <form  class="form-inline" action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float: right">
        <span style="color: red; font-weight: bold">${error}</span>
        <input type="text" name="queryBookName" class="form-control" placeholder="请输入要查询的书籍名称" value="${book.bookName}" >
        <input type="submit" class="btn btn-primary" value="查询">
    </form>

</div>
```

现在文本输入框和按钮就并排了~

剩下还有一些功能需要添加，拦截器（用户登录审核等等，）文件上传下载，ajax等等



#### 关于Ajax：

我们调用url的时候，无非是普通转发或者重定向，那么，打开百度一下，

输入一些文字，他会在不刷新当前页面的情况下更新部分网页，最明显的就是会出现一堆相关文字在搜索框内；

这就是Ajax，可以在不刷新页面下实现局部更新！

使用Ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新；

（具体一些表现就是地址栏内的url不变，同时又可以实现刷新



@RestController注解相当于@ResponseBody加 @Controller合在一起的作用

例如：如果只用@Controller注释类，那么类下的函数返回的只能是页面类的视图数据，如果

是RestController还可以返回别的类型，比如String



##### jQuery.ajax



jquery官网下载相关js文件，点击链接出现js的内容是正常的，ctrl+s保存就好；

可以web下建包statics->js->jquery.js

需要在resource下资源文件applicationContext中添加静态资源过滤，不然无法加载js

```
<!--静态资源过滤 -->
<mvc:default-servlet-handler />
```

编写测试index.jsp:



```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>

    <%--<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>--%>
    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.1.1.min.js"></script>
    <script>
      function a1(){
        $.post({
          url:"${pageContext.request.contextPath}/a1",
          data:{'name':$("#txtName").val()},
          success:function (data,status) {
            alert(data);
            alert(status);
          }
        });
      }
    </script>

  </head>
  <body>
  $END$

  <%--onblur：失去焦点触发事件--%>
  用户名:<input type="text" id="txtName" onblur="a1()"/>

  </body>
</html>
```

注意：数据来源于事件a1内的data，而不是input那个文本框

jQuery是一个库，js的大量函数（方法）

xhr：是ajax请求

从层次上讲，后端负责将json格式传给前端，前端获取并进行处理巴拉巴拉

js需要了解：

- 函数闭包等等
- Dom
- Bom
- ES6..



好了，还是先说ajax，简单实现的话流程就是后端传递（提供）一个字符串

然后前端ajax发起请求，利用url提交参数然后获取回调函数（callback）



可以通过一个用户-密码文本框提示信息来熟悉ajax：

注意类用注释@RestController注释，这样可以返回json字符串格式，更方便

```java
@RequestMapping("/a3")
public String ajax3(String name,String pwd){
    String msg = "";
    //模拟数据库中存在数据
    if (name!=null){
        if ("admin".equals(name)){
            msg = "OK";
        }else {
            msg = "用户名输入错误";
        }
    }
    if (pwd!=null){
        if ("123456".equals(pwd)){
            msg = "OK";
        }else {
            msg = "密码输入有误";
        }
    }
    return msg; //由于@RestController注解，将msg转成json格式返回
}
```



相关页面：login.jsp

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>ajax</title>
    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.5.1.js"></script>
    <script>

        function a1(){
            $.post({
                url:"${pageContext.request.contextPath}/a3",
                data:{'name':$("#name").val()},
                success:function (data) {
                    if (data.toString()=='OK'){
                        $("#userInfo").css("color","green");
                    }else {
                        $("#userInfo").css("color","red");
                    }
                    $("#userInfo").html(data);
                }
            });
        }
        function a2(){
            $.post({
                url:"${pageContext.request.contextPath}/a3",
                data:{'pwd':$("#pwd").val()},
                success:function (data) {
                    if (data.toString()=='OK'){
                        $("#pwdInfo").css("color","green");
                    }else {
                        $("#pwdInfo").css("color","red");
                    }
                    $("#pwdInfo").html(data);
                }
            });
        }

    </script>
</head>
<body>
<p>
    用户名:<input type="text" id="name" onblur="a1()"/>
    <span id="userInfo"></span>
</p>
<p>
    密码:<input type="text" id="pwd" onblur="a2()"/>
    <span id="pwdInfo"></span>
</p>
</body>
</html>
```

当用户框内不是admin时鼠标移开后会提示错误，密码同理；

另外，还需要在applicationContext中添加json过滤配置，不然提示信息会乱码：

```xml
<!--json乱码统一解决 -->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

部分参数含义如下：

```txt
jQuery.ajax(...)
      部分参数：
            url：请求地址
            type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
            data：要发送的数据
    contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        timeout：设置请求超时时间（毫秒）
      beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

其中 $("#pwdInfo").html(data);

是jquery的语法，给id为pwdInfo的dom标签赋值为data;

也就是给提示信息赋值





#### 拦截器

和过滤器类似不过拦截器是AOP思想的具体应用，

过滤器是servlet规范的一部分，当url-pattern为/*时可以对所以要访问的资源进行拦截，

可以访问jsp文件；



拦截器是springMVC框架的，拦截器只会拦截访问的控制器方法，

如果访问jsp/html/css/image/js是不会进行拦截的



新建子项目-06

建立controller部分和config部分，

config下新建MyInterceptor:



验证用户是否登录（认证用户）：

1、有一个登陆页面，需要写一个controller访问页面。

2、登陆页面有一提交表单的动作。需要在controller中处理。判断用户名密码是否正确。如果正确，向session中写入用户信息。*返回登陆成功。*

3、拦截用户请求，判断用户是否登陆。如果用户已经登陆。放行， 如果用户未登陆，跳转到登陆页面

**测试：**

1、编写一个登陆页面  login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Title</title>
</head>

<h1>登录页面</h1>
<hr>

<body>
<form action="${pageContext.request.contextPath}/user/login">
  用户名：<input type="text" name="username"> <br>
  密码：<input type="password" name="pwd"> <br>
   <input type="submit" value="提交">
</form>
</body>
</html>
```

2、编写一个Controller处理请求

```java
package com.kuang.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpSession;

@Controller
@RequestMapping("/user")
public class UserController {

   //跳转到登陆页面
   @RequestMapping("/jumplogin")
   public String jumpLogin() throws Exception {
       return "login";
  }

   //跳转到成功页面
   @RequestMapping("/jumpSuccess")
   public String jumpSuccess() throws Exception {
       return "success";
  }

   //登陆提交
   @RequestMapping("/login")
   public String login(HttpSession session, String username, String pwd) throws Exception {
       // 向session记录用户身份信息
       System.out.println("接收前端==="+username);
       session.setAttribute("user", username);
       return "success";
  }

   //退出登陆
   @RequestMapping("logout")
   public String logout(HttpSession session) throws Exception {
       // session 过期
       session.invalidate();
       return "login";
  }
}
```

3、编写一个登陆成功的页面 success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Title</title>
</head>
<body>

<h1>登录成功页面</h1>
<hr>

${user}
<a href="${pageContext.request.contextPath}/user/logout">注销</a>
</body>
</html>
```

4、在 index 页面上测试跳转！启动Tomcat 测试，未登录也可以进入主页！

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
 <head>
   <title>$Title$</title>
 </head>
 <body>
 <h1>首页</h1>
 <hr>
<%--登录--%>
 <a href="${pageContext.request.contextPath}/user/jumplogin">登录</a>
 <a href="${pageContext.request.contextPath}/user/jumpSuccess">成功页面</a>
 </body>
</html>
```

发现请求不会被拦截，测试正常，注意success和login应该放在WEB-INF下，这样才能扫描到;

然后添加拦截器：

5、编写用户登录拦截器

```java
package com.kuang.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class LoginInterceptor implements HandlerInterceptor {

   public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
       // 如果是登陆页面则放行
       System.out.println("uri: " + request.getRequestURI());
       if (request.getRequestURI().contains("login")) {
           return true;
      }

       HttpSession session = request.getSession();

       // 如果用户已登陆也放行
       if(session.getAttribute("user") != null) {
           return true;
      }

       // 用户没有登陆跳转到登陆页面
       request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
       return false;
  }

   public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

  }
   
   public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

  }
}
```



6、在Springmvc的配置文件中注册拦截器

```xml
<!--关于拦截器的配置-->
<mvc:interceptors>
   <mvc:interceptor>
       <mvc:mapping path="/**"/>
       <bean id="loginInterceptor" class="com.wang.interceptor.LoginInterceptor"/>
   </mvc:interceptor>
</mvc:interceptors>
```

如果getRequestURI 返回ture，就是放行，不拦截，false就是不放行



#### 文件上传和下载



文件上传是项目开发中最常见的功能之一 ,springMVC 可以很好的支持文件上传，但是SpringMVC上下文中默认没有装配MultipartResolver，因此默认情况下其不能处理文件上传工作。如果想使用Spring的文件上传功能，则需要在上下文中配置MultipartResolver。

前端表单要求：为了能上传文件，必须将表单的method设置为POST，并将enctype设置为multipart/form-data。只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器；



需要了解一个表单中的属性enctype：enctype="xxx":

- application/x-www=form-urlencoded：默认方式，只处理表单域中的 value 属性值，采用这种编码方式的表单会将表单域中的值处理成 URL 编码方式。
- multipart/form-data：这种编码方式会以二进制流的方式来处理表单数据，这种编码方式会把文件域指定文件的内容也封装到请求参数中，不会对字符编码。
- text/plain：除了把空格转换为 "+" 号外，其他字符都不做编码处理，这种方式适用直接通过表单发送邮件。

1、导入文件上传的jar包，commons-fileupload ， Maven会自动帮我们导入他的依赖包 commons-io包；

```xml
<!--文件上传-->
<dependency>
   <groupId>commons-fileupload</groupId>
   <artifactId>commons-fileupload</artifactId>
   <version>1.3.3</version>
</dependency>
<!--servlet-api导入高版本的-->
<dependency>
   <groupId>javax.servlet</groupId>
   <artifactId>javax.servlet-api</artifactId>
   <version>4.0.1</version>
</dependency>
```

2、配置bean：multipartResolver

【**注意！！！这个bena的id必须为：multipartResolver ， 否则上传文件会报400的错误！在这里栽过坑,教训！**】

```xml
<!--文件上传配置-->
<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
   <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
   <property name="defaultEncoding" value="utf-8"/>
   <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
   <property name="maxUploadSize" value="10485760"/>
   <property name="maxInMemorySize" value="40960"/>
</bean>
```

CommonsMultipartFile 的 常用方法：

- **String getOriginalFilename()：获取上传文件的原名**
- **InputStream getInputStream()：获取文件流**
- **void transferTo(File dest)：将上传文件保存到一个目录文件中**

 我们去实际测试一下

3、编写前端页面

```jsp
<form action="${pageContext.request.contextPath}/upload" enctype="multipart/form-data" method="post">
 <input type="file" name="file"/>
 <input type="submit" value="upload">
</form>
```

4、**Controller**

```java
package com.wang.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.*;

@RestController
public class FileController {
   //@RequestParam("file") 将name=file控件得到的文件封装成CommonsMultipartFile 对象
   //批量上传CommonsMultipartFile则为数组即可
   @RequestMapping("/upload")
   public String fileUpload(@RequestParam("file") CommonsMultipartFile file , HttpServletRequest request) throws IOException {

       //获取文件名 : file.getOriginalFilename();
       String uploadFileName = file.getOriginalFilename();

       //如果文件名为空，直接回到首页！
       if ("".equals(uploadFileName)){
           return "redirect:/index.jsp";
      }
       System.out.println("上传文件名 : "+uploadFileName);

       //上传路径保存设置
       String path = request.getServletContext().getRealPath("/upload");
       //如果路径不存在，创建一个
       File realPath = new File(path);
       if (!realPath.exists()){
           realPath.mkdir();
      }
       System.out.println("上传文件保存地址："+realPath);

       InputStream is = file.getInputStream(); //文件输入流
       OutputStream os = new FileOutputStream(new File(realPath,uploadFileName)); //文件输出流

       //读取写出
       int len=0;
       byte[] buffer = new byte[1024];
       while ((len=is.read(buffer))!=-1){
           os.write(buffer,0,len);
           os.flush();
      }
       os.close();
       is.close();
       return "redirect:/index.jsp";
  }
}
```

5、测试上传文件，OK！



**采用file.Transto 来保存上传的文件**

1、编写Controller

```java
/*
* 采用file.Transto 来保存上传的文件
*/
@RequestMapping("/upload2")
public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {

   //上传路径保存设置
   String path = request.getServletContext().getRealPath("/upload");
   File realPath = new File(path);
   if (!realPath.exists()){
       realPath.mkdir();
  }
   //上传文件地址
   System.out.println("上传文件保存地址："+realPath);

   //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
   file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));

   return "redirect:/index.jsp";
}
```

2、前端表单提交地址修改

3、访问提交测试，OK！



运行后上传的文件可以在当前绝对路径下的upload文件夹下，可以在项目中out文件夹下对应子项目的upload内找到；



然后就是下载：一般下载都是点击一个链接然后我们就可以下载了！

**文件下载步骤：**

1、设置 response 响应头

2、读取文件 -- InputStream

3、写出文件 -- OutputStream

4、执行操作

5、关闭流 （先开后关）

**代码实现：**

```java
@RequestMapping(value="/download")
public String downloads(HttpServletResponse response ,HttpServletRequest request) throws Exception{
   //要下载的图片地址
   String  path = request.getServletContext().getRealPath("/upload");
   String  fileName = "lsp.jpg";

   //1、设置response 响应头
   response.reset(); //设置页面不缓存,清空buffer
   response.setCharacterEncoding("UTF-8"); //字符编码
   response.setContentType("multipart/form-data"); //二进制传输数据
   //设置响应头
   response.setHeader("Content-Disposition",
           "attachment;fileName="+URLEncoder.encode(fileName, "UTF-8"));

   File file = new File(path,fileName);
   //2、 读取文件--输入流
   InputStream input=new FileInputStream(file);
   //3、 写出文件--输出流
   OutputStream out = response.getOutputStream();

   byte[] buff =new byte[1024];
   int index=0;
   //4、执行 写出操作
   while((index= input.read(buff))!= -1){
       out.write(buff, 0, index);
       out.flush();
  }
   out.close();
   input.close();
   return null;
}
```

前端

```jsp
<a href="/download">点击下载</a>
```

测试，下载OK

ps：这个提供下载的文件依然是放在upload下









