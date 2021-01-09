# SpringMVC 

## 一、Spring MVC简介

​	Spring 为展现层提供的基于 MVC 设计理念的优秀的 ,Web 框架，是目前最主流的 MVC 框架之一,Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请求的控制器，而无须实现任何接口,支持 REST 风格的 URL 请求;

### 1.1 前期准备

**1、添加相关依赖**

```xml
<dependencies>
    <!--基础依赖-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.1</version>
        <scope>provided</scope>
    </dependency>

    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.1.1</version>
    </dependency>


    <!--    SpringMVC 相关依赖-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>
    
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

2、配置Spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--民名空间的引入需要特别注意-->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--    配置自定扫描包-->
    <context:component-scan base-package="com.restart"/>

    <!--    配置视图解析器：如何把handler 方法的返回值解析为是假的物理视图-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>


</beans>
```

3、配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <!-- 配置 DispatcherServlet -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置 DispatcherServlet 的一个初始化参数: 配置 SpringMVC 配置文件的位置和名称 -->
        <!--
            实际上也可以不通过 contextConfigLocation 来配置 SpringMVC 的配置文件, 而使用默认的.
            默认的配置文件为: /WEB-INF/<servlet-name>-servlet.xml
        -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>

        <!--    表示记载配置文件时，就载入相关的类-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

4、操作类

```java
@Controller
public class HelloWord {
    /**
     *1 、 使用@requestMapping配置请求处理器
     * 2、返回值会通过视图解析器，解析为实际视图，对于，InternalResourceViewResolver
     * 通过 prefix+returnval+后缀 这样得到实际视图
     * /WEB-INF/views/success.jsp
     * @return
     */
    @RequestMapping("/helloword")
    public String hello(){
        System.out.println("hello word");
        return "success";
    }
}
```

## 二、RequestMapping 

### 2.1 RerquestMapping 相关处理	

Spring MVC 使用 @RequestMapping 注解为控制器指定可以处理哪些 URL 请求;

在控制器的类定义及方法定义处都可标注;

@RequestMapping

- 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;

- 方法处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;

```java
@RequestMapping
@Controller
public class SpringMvcTest {
    /**
     * requestMapping 除了可以修饰方法还可以修饰类
     * 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;
     * 方法定义处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;
     * @return
     */
    @RequestMapping("/testrequestMapping")
    public String testRequestMaping(){
        System.out.println("success");
        return "success";
    }
}
```

### 2.2 RequestMapping 参数详解

```java
@RequestMapping("/springmvc")
@Controller
public class SpringMvcTest {
    /**
     * requestMapping 除了可以修饰方法还可以修饰类
     * 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;
     * 方法定义处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;
     * @return
     */
    @RequestMapping("/testmapping")
    public String testRequestMaping(){
        System.out.println("success");
        return "success";
    }

    /**
     * 使用method 指定请求的参数
     * @return
     */
    @RequestMapping(value = "/methodtest",method = RequestMethod.GET)
    public String testRequestMapingMethod(){
        System.out.println("method test");
        return "success";
    }

    /**
     *–
     * !param1: 表示请求不能包含名为 param1 的请求参数 –
     * param1 != value1: 表示请求包含名为 param1 的请求参数，但其– 值
     * 不能为 value1
     * {“param1=value1”, “param2”}: 请求必须包含名为 param1 和param2 –
     * 的两个请求参数，且 param1 参数的值必须为 value1
     * 
     * header 与Params 的用法相似
     * @return
     */
    @RequestMapping(value = "/paramtest",method = RequestMethod.GET,params={"username","age!=10"})
    public String testRequest(){
        System.out.println("method test");
        return "success";
    }


}
```

### 2.3  @PathVariable



```java
/**
 * 可以映射url中的占位符到目标方法中；
 *正因为这种特性，使得SpringMVC 支持REST 风格；
 *
 * REST Representional State Trandfer ,资源表现层状态化，
 * 是目前鼻尖流行的一种互联网软件价格，她的结构清晰
 * 资源：网络上的实体，或者说网络上的具体信息，（文本，图片，）可以用一个URI 来表示
 
 * 表现层：把资源具体呈现出来的形式，叫做它的表现层 
 * （Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格
 * 式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
 *
 
 * 状态转化：（State Transfer）：每• 发出一个请求，就代表了客户端和服务器的一
 * 次交互过程。HTTP协议，是一个无状态协议，即所有的状态都保存在服务器
 * 端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“
 * 状态转化”（State Transfer）。而这种转化是建立在表现层之上的，所以就是 “
 * 表现层状态转化”。具体说，就是 HTTP 协议里面，四个表示操作方式的动
 * 词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获
 * 取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。
 *
 *
 * @param id
 * @return
 */
@RequestMapping(value = "/variabletest/{id}", method = RequestMethod.GET)
public String testPathVariable(@PathVariable("id") Integer id) {
    System.out.println("testPathVariable"+" "+id);
    return SUCCESS;
}
```

 * REST (Representional State Trandfer ),资源表现层状态化，是目前鼻尖流行的一种互联网软件价格，她的结构清晰
 * **资源**：网络上的实体，或者说网络上的具体信息，（文本，图片，）可以用一个URI 来表示

 * **表现层**：把资源具体呈现出来的形式，叫做它的表现层 （Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
 * **状态转化**：（State Transfer）：每发出一个请求，就代表了客户端和服务器的一次交互过程。HTTP协议，是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”（State Transfer）。而这种转化是建立在表现层之上的，所以就是 “表现层状态转化”。具体说，**就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。**



### 2.4 **HiddenHttpMethodFilter**

​	 **HiddenHttpMethodFilter**：浏览器 form 表单只支持 GET 与 POST 请求，而DELETE、PUT 等 method 并不支持，Spring3.0 添加了一个过滤器，可以将这些请求转换为标准的 http 方法，使得支持 GET、POST、PUT 与DELETE 请求。

```xml
<!--    配置HiddenHttpMethodFilter 过滤器，可以将post请求转换为 post请求转化为 put delete 请求-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```html
<form action="springmvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="PUT"/>
    <input type="submit" value="TestRest PUT"/>
</form>
<br>

<form action="springmvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="DELETE"/>
    <input type="submit" value="TestRest DELETE"/>
</form>
<br>

<form action="springmvc/testRest" method="post">
    <input type="submit" value="TestRest Post"/>
</form>
<br>
```

```java
/**
 * Rest 风格的URL
 * 以CRUD 为例：
 * 增 /order POST        POST
 * 删除  /order/1 DELETE  delete?id=1
 * 改   /order/1  PUT    update?id=1
 * 查   /order/1 GET     get?id=1
 *
 * 如何发送PUT和DELETE请求
 * 1、配置 HiddenHttpMethodFilter
 * 2、发送POST 请求
 * 3、在需要发送POST请求时携带 name="_method"的隐藏域，值为 DELETE 或者PUT
 *
 * 在SpringMVC的方法中如何得到id呢？
 * 使用@PathVariable 注解
 *
 *
 * @param id
 * @return
 */
@RequestMapping(value = "/testRest/{id}",method = RequestMethod.PUT)
public String testRestPut(@PathVariable Integer id){
    System.out.println("test restPut"+" "+id);
    return SUCCESS;
}


@RequestMapping(value = "/testRest/{id}",method = RequestMethod.DELETE)
public String testRestDelete(@PathVariable Integer id){
    System.out.println("test restDelete"+" "+id);
    return SUCCESS;
}


@RequestMapping(value = "/testRest",method = RequestMethod.POST)
public String testRest(){
    System.out.println("test restRest POST");
    return SUCCESS;
}


@RequestMapping(value = "/testRest/{id}",method = RequestMethod.GET)
public String testRest(@PathVariable Integer id){
    System.out.println("test restGet"+" "+id);
    return SUCCESS;
}
```

## 三、映射请求参数&请求参数

​	Spring MVC 通过分析处理方法的签名，将 HTTP 请求信 息绑定到处理方法的相应人参中。Spring MVC 对控制器处理方法签名的限制是很宽松的， 几乎可以按喜欢的任何方式对方法进行签名。必要 时可以对方法及方法入参标注相应的注解（@PathVariable@RequestParam、@RequestHeader 等）、Spring MVC 框架会将 HTTP 请求的信息绑定到相应的方法入参中，并根据方法的返回值类型做出相应的后续处理。

### 3.1 使用 @RequestParam 绑定请求参数值

```html
<a href="springmvc/testrequestparam?username=cen&age=12">test requestparam</a><br>
```

```java
/**
 * @RequsetParam 来映射请求参数
 * value 为请求参数的参数名
 * required 该参数，是否必须
 * defaultValue 默认值
 */
@RequestMapping(value = "/testrequestparam")
public String testRequestParam(@RequestParam(value = "username",required = false) String username,
                               @RequestParam(value = "age",required = false,defaultValue = "0") Integer age) {
    System.out.println("testrequestparam"+" "+ " username:"+username);
    System.out.println("testrequestparam"+" "+ " age:"+age);
    return SUCCESS;
}
```

### 3.2 使用 @RequestHeader 绑定请求报头的属性值

```html
<a href="springmvc/testrequestheader">test requestheader</a><br>
```

```java
/**
 * @RequestHeader 处理请求头相关信息
 */
@RequestMapping(value = "/testrequestheader")
public String testRequestHeader(@RequestHeader(value = "Accept-Language") String Accept){
    System.out.println("testRequestHeader,Accept-Language:"+Accept);
    return SUCCESS;
}
```

### 3.3使用 @CookieValue 绑定请求中的 Cookie 值

```html
<a href="springmvc/testcookievalue">test cookievalue</a><br>
```

```java
@RequestMapping(value = "/testcookievalue")
public String testCookieValue(@CookieValue(value = "JSESSIONID") String SessionID){
    System.out.println("testCookieValue:sessionId"+SessionID);
    return SUCCESS;
}
```

### 3.4 使用 POJO 对象绑定请求参数值

Spring MVC 会按请求参数名和 POJO 属性名进行自动匹配，自动为该对象填充属性值。支持级联属性。

```java
package com.restart.beans;

/**
 * @author : anner
 * @description:
 * @date : 2021/1/9
 */
public class User {
    private Integer id;

    private String username;
    private String password;

    private String email;
    private int age;

    private Address address;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }
    
    @Override
    public String toString() {
        return "User [id=" + id + ", username=" + username + ", password="
                + password + ", email=" + email + ", age=" + age + "]";
    }

    public User(String username, String password, String email, int age) {
        super();
        this.username = username;
        this.password = password;
        this.email = email;
        this.age = age;
    }

    public User(Integer id, String username, String password, String email,
                int age) {
        super();
        this.id = id;
        this.username = username;
        this.password = password;
        this.email = email;
        this.age = age;
    }

    public User() {}

}




public class Address {
    private String province;
    private String city;

    public String getProvince() {
        return province;
    }

    public void setProvince(String province) {
        this.province = province;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    @Override
    public String toString() {
        return "Address [province=" + province + ", city=" + city + "]";
    }

}

```

```html
<form action="springmvc/testpojo" method="post">
            username: <input type="text" name="username"/>
    <br>
            password: <input type="password" name="password"/>
    <br>
            email: <input type="text" name="email"/>
    <br>
            age: <input type="text" name="age"/>
    <br>
            city: <input type="text" name="address.city"/>
    <br>
            province: <input type="text" name="address.province"/>
    <br>
    <input type="submit" value="Submit"/>
</form>
```

```java
/**
 *
 *SpringMVC  会按照参数名和POJO的属性进行自动的匹配
 * 自动为该对象填充属性值，并且支持级连属性
 *
 */
@RequestMapping(value = "/testpojo")
public String testPOJO(User user){
    System.out.println("test pojo:"+user);
    return SUCCESS;
}
```

### 3.5 使用 Servlet API 作为入参

- HttpServletRequest 

- HttpServletResponse 

- HttpSession 

- java.security.Principal 

- Locale 

- InputStream 

- OutputStream 

- Reader 

- Writer

## 四、处理模型数据

