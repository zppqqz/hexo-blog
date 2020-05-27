Spring入门-基本注解的使用



# [Spring入门-基本注解的使用](https://www.cnblogs.com/youngchaolin/p/11345370.html)



**目录**

- [组件扫描](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label0)
- 基本注解使用
  - [（1）创建对象、作用域、延迟加载、生命周期使用注解](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label1_0)
  - [（2）使用@Autowired和@Qualifier依赖注入](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label1_1)
  - [（3）使用@Resource依赖注入 ](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label1_2)
  - [（3）使用@Value注入值](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label1_3)
- [结论](https://www.cnblogs.com/youngchaolin/p/11345370.html#_label2)

 

**引言**

**前面创建和管理对象使用xml配置文件来完成，但是随着对象的增多，xml配置文件也越来越大，为了防止这种现象的发生，注解的使用就应运而生**。注解是在类或方法前面添加特定以"@+注解名"开头的标识，实现xml配置文件的功能，如前面学习的创建对象、配置作用域、配置延迟加载等功能等。

[回到顶部](https://www.cnblogs.com/youngchaolin/p/11345370.html#_labelTop)

### 组件扫描

容器会扫描指定的包及子包下面所有的类，如果该类前面有特定的注解，比如@Component，则容器会将其纳入管理，不需要在xml中配置bean，其作用相当于在配置文件中配置一个bean元素，bean的id是带注解@Component的类的第一个字母小写后的名称。

组件扫描的使用方法：

step1 在类面前添加特定的注解，比如@Component，默认的id是类名首字母小写后的名称

step2 在我们的配置文件当中，配置组件扫描

```
1 <context:component-scan base-package="package_name"/> <!--其中package_name代表我们要扫描的包-->
```

只有在组件类定义前面有如下注解标记时，才会扫描到Spring容器来管理，为后续使用其他注解提供必要条件。

@Component 通用注释，spring框架的，Rod Johnson大神写的

@Named 通用注释，SUN公司的，模仿Spring的注释，现在很少用，需要导额外的包

@Repository 持久化层组件注解，一般用于访问数据库

@Service 业务层组件注解，用于业务逻辑处理  

@Controller 控制层组件注解

[回到顶部](https://www.cnblogs.com/youngchaolin/p/11345370.html#_labelTop)

### 基本注解使用

后期做项目时，如果是控制层的类需加注解@Controller，如果是业务层的类需加@Service，如果是持久层的类需加@Repository，其他类使用@Component来添加。这里测试都是使用@Component来完成。

前期准备工作，是配置组件扫描，扫描我的测试包com.boe。

```
1         <!-- 配置组件扫描 -->    
2         <context:component-scan base-package="com.boe"></context:component-scan>
```



#### （1）创建对象、作用域、延迟加载、生命周期使用注解

创建一个People类来使用注解。

1 package com.boe;
 2 
 3 import javax.annotation.PostConstruct;
 4 import javax.annotation.PreDestroy;
 5 
 6 import org.springframework.context.annotation.Lazy;
 7 import org.springframework.context.annotation.Scope;
 8 import org.springframework.stereotype.Component;
 9 
10 @Component("p")
11 @Scope("singleton")
12 @Lazy(false) 
13 public class People {
14     //默认构造方法
15     public People() {
16         System.out.println("创建一个人");
17     }
18     
19     //初始化方法和销毁方法的注解，需导入tomcat
20     @PostConstruct 
21     public void init() {
22         System.out.println("初始化了");
23     }
24     @PreDestroy
25     public void destroy() {
26         System.out.println("销毁了");
27     }    
28 }

junit测试

1     //测试@Component、作用域、延迟加载和生命周期
2     @Test
3     public void test1() {
4         String path="myIOC.xml";
5         AbstractApplicationContext ac=new ClassPathXmlApplicationContext(path);
6         People people=ac.getBean("p",People.class);
7         System.out.println(people);
8         ac.close();
9     }

测试结果

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190813130023988-236400170.png)

可以看出通过注解就可以创建对象，如图是在作用域为singleton，延迟加载为false的情况下结果，其跟前面使用xml配置文件的效果是一样的。需要注意的是配置销毁方法只对singleton有效。



#### （2）使用@Autowired和@Qualifier依赖注入

这种方法适用于set方法和构造器方法注入，注解的位置可以写在属性前面，也可以写在set或构造器方法前面，区别就是前者只能注入，后者是可以在set或构造器方法里添加其他的逻辑。默认情况下是byType类型注入，如果要指定名字需要配合@Qualifier("name")来使用。

案例通过将people注入到国家类和省类对象中来完成。

case1 set方法注入，将people注入到country

Country类

1 package com.boe;
 2 
 3 import org.springframework.beans.factory.annotation.Autowired;
 4 import org.springframework.beans.factory.annotation.Qualifier;
 5 import org.springframework.stereotype.Component;
 6 
 7 @Component
 8 public class Country {
 9     //属性
10     private People people;
11     //get set方法
12     public People getPeople() {
13         return people;
14     }
15     @Autowired
16     public void setPeople(@Qualifier("p")People people) {
17         this.people = people;
18     }
19     //构造方法
20     public Country() {
21         System.out.println("创建一个新的国家");
22     }
23     //重写toString方法
24     @Override
25     public String toString() {
26         return "Country [people=" + people + "]";
27     }
28 }

junit测试

1     //测试使用@Autowired和@Qualifier注解-set方法注入
2     @Test
3     public void test2() {
4         String path="myIOC.xml";
5         ApplicationContext ac=new ClassPathXmlApplicationContext(path);
6         Country c=ac.getBean("country",Country.class);
7         System.out.println(c);
8     }

测试结果

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190813131755417-1949539835.png)

case2 构造器注入，将people注入到province

Province类

1 package com.boe;
 2 
 3 import org.springframework.beans.factory.annotation.Autowired;
 4 import org.springframework.beans.factory.annotation.Qualifier;
 5 import org.springframework.stereotype.Component;
 6 
 7 @Component
 8 public class Province {
 9     //属性
10     private People people;
11     //带参数构造方法
12     @Autowired
13     public Province(@Qualifier("p")People people) {
14         System.out.println("新建一个省，有人民");
15         this.people = people;
16     }
17     //默认构造方法
18     public Province() {
19         System.out.println("新建一个省，没有人民");
20     }
21     //重写toString
22     @Override
23     public String toString() {
24         return "Province [people=" + people + "]";
25     }    
26 }

junit测试

1     //测试使用@Autowired和@Qualifier注解-构造方法注入
2     @Test
3     public void test3() {
4         String path="myIOC.xml";
5         ApplicationContext ac=new ClassPathXmlApplicationContext(path);
6         Province province=ac.getBean("province",Province.class);
7         System.out.println(province);
8     }

测试结果

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190813132035206-1261042808.png)



#### （3）使用@Resource依赖注入 

这种注解只适合set方法注入，并且默认情况下是byName类型，如果@Resource(name="")里没有添加名字或name找不到，则按照byType方式。

案例通过将people注入到县类对象中来完成。

```
1 package com.boe;
 2 
 3 import javax.annotation.Resource;
 4 
 5 import org.springframework.stereotype.Component;
 6 
 7 @Component
 8 public class Village {
 9     //属性
10     private People people;
11     //set方法
12     @Resource(name="p")
13     public void setPeople(People people) {
14         this.people = people;
15     }
16     //构造方法
17     public Village() {
18         System.out.println("新建了一个村");
19     }
20     //重写
21     @Override
22     public String toString() {
23         return "Village [people=" + people + "]";
24     }    
25 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

junit测试

![img](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1     //测试使用@Resource-set方法注入
2     @Test
3     public void test4() {
4         String path="myIOC.xml";
5         ApplicationContext ac=new ClassPathXmlApplicationContext(path);
6         Village v=ac.getBean("village",Village.class);
7         System.out.println(v);
8     }
```

测试结果

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190813132558869-1240846172.png)



#### （3）使用@Value注入值

使用@Value注解，配合spring表达式的使用，可以将值注入到对象。

UserBean类

```
package com.boe;
 2 
 3 import org.springframework.beans.factory.annotation.Value;
 4 import org.springframework.stereotype.Component;
 5 
 6 @Component("user")
 7 public class UserBean {
 8     @Value("clyang") //name赋值为clyang
 9     //@Value("")也可以用在set方法前，区别就是属性前使用只能赋值，如果set方法前写除了赋值外还可以写其他的功能
10     private String name;
11     //读取properties属性文件
12     @Value("#{config.pageSize}") //使用spring表达式
13     private String pageSize;
14     
15     public String getName() {
16         return name;
17     }
18     public void setName(String name) {
19         this.name = name;
20     }
21     public String getPageSize() {
22         return pageSize;
23     }
24     public void setPageSize(String pageSize) {
25         this.pageSize = pageSize;
26     }
27     @Override
28     public String toString() {
29         return "UserBean [name=" + name + ", pageSize=" + pageSize + "]";
30     }
31 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

xml配置文件中添加对properties文件的读取

```
<!-- 读取properties属性文件 -->
<util:properties id="config" location="classpath:config.properties"></util:properties>
```

junit测试

![img](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1     //测试用@Value注解，注入值（基本数据类型或者集合）
 2     @Test
 3     public void test5() {
 4         //路径配置文件
 5         String path="myIOC.xml";
 6         //启动spring容器，使用AbstractApplicationContext子接口
 7         ApplicationContext ac=new ClassPathXmlApplicationContext(path);
 8         //得到userBean
 9         UserBean ub=ac.getBean("user", UserBean.class);
10         System.out.println(ub);        
11     }
```

测试结果

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190813133223028-1201724929.png)

 



### 结论

（1）使用注解可以减少xml配置文件中的内容，并且可以实现跟xml一样的功能，原则是能使用注解的尽量使用注解，不能使用的就使用配置文件完成。

（2）@Resource默认采用byName类型注入，@Autowired默认采用byType，如果要改byName需配合@Qualifier一起使用

（3）@Resource来自JaveEE，需要有包annotation，@Autowired和@Qualifier来自Spring，参考下图

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190815092029458-681893238.png)

![img](https://img2018.cnblogs.com/blog/1486105/201908/1486105-20190815092120429-1424355555.png)

 

参考博文

（1）https://www.cnblogs.com/youngchaolin/p/11332783.html

（2）https://www.cnblogs.com/Jason-Xiang/p/5345342.html