# 分页插件（Pagehelper）

https://blog.csdn.net/cs_hnu_scw/article/details/80718467

# 情景一：（SpringBoot 和 Mybatis环境）

（1）在pom.xml文件中引入依赖库

<!-- 分页插件 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper-spring-boot-starter</artifactId>
			<version>1.2.3</version>
		</dependency>
（2）在application.properties中添加分页配置

# 配置pageHelper分页插件的内容
pagehelper.helper-dialect=mysql
pagehelper.reasonable=true
pagehelper.support-methods-arguments=true
pagehelper.params=count=countSql
或者在application.yml文件中添加分页配置

pagehelper:
 helperDialect: mysql
 reasonable: true
 supportMethodsArguments: true
 params: count=countSql



·helperDialect:

配置使用哪种数据库语言，不配置的话pageHelper也会自动检测

·reasonable:

配置分页参数合理化功能，默认是false。 #启用合理化时，如果pageNum<1会查询第一页，如果pageNum>总页数会查询最后一页； #禁用合理化时，如果pageNum<1或pageNum>总页数会返回空数据。

·params:

为了支持startPage(Object params)方法，增加了该参数来配置参数映射，用于从对象中根据属性名取值; 可以配置 pageNum,pageSize,count,pageSizeZero,reasonable，不配置映射的用默认值， 默认值为pageNum=pageNum;pageSize=pageSize;count=countSql;reasonable=reasonable;pageSizeZero=pageSizeZero。

·supportMethodsArguments:

支持通过Mapper接口参数来传递分页参数，默认值false，分页插件会从查询方法的参数值中，自动根据上面 params 配置的字段中取值，查找到合适的值时就会自动分页。




mapper层写上查询该表数据返回List的方法：

//查询所有
List<User> queryUserInfo();
在service以及impl也写上相关方法：

//查询所有
List<User> queryUserInfo();
@Override
public List<User> queryUserInfo() {

    return userMapper.queryUserInfo();
}
相关的mapper.xml就不做过多介绍了，就是一个查询。

 

ok，接下来开始使用分页插件，配合上面的查询实现分页查询：

在userService上添加 分页查询方法2个：

List<User> findAllUserByPageF(int pageNum,int pageSize);

PageInfo<User> findAllUserByPageS(int pageNum, int pageSize);
userServiceImpl上添加 分页查询实现：

@Override
public List<User> findAllUserByPageF(int pageNum, int pageSize) {
    // TODO Auto-generated method stub
    PageHelper.startPage(pageNum, pageSize);
    List<User> lists = userMapper.queryUserInfo();
    return lists;
}

@Override
public PageInfo<User> findAllUserByPageS(int pageNum, int pageSize) {
    // TODO Auto-generated method stub
    PageHelper.startPage(pageNum, pageSize);
    List<User> lists = userMapper.queryUserInfo();
    PageInfo<User> pageInfo = new PageInfo<User>(lists);
    return pageInfo;

}
最后在controller写上测试接口，

@GetMapping("/testPageHelper1")
public PageInfo<User> testPageHelper1(){
    PageInfo<User> queryResult = userService.findAllUserByPageS(1, 5);
    return queryResult;
}

@GetMapping("/testPageHelper2")
public List<User> testPageHelper2(){
    List<User> queryResult = userService.findAllUserByPageF(1, 5);
    return queryResult;
}


然后用POSTMAN 分别调接口看看结果：




![image-20200518171153562](C:\Users\zpp\AppData\Roaming\Typora\typora-user-images\image-20200518171153562.png)























方法二：使用最原始的形式（SpringBoot+Mybatis配置文件的形式，也就是整合环境还是利用xml的形式搭建的，但是都是通过@configuration注解开发类）
使用步骤：

（1）在pom.xml文件中，添加分页插件的依赖（注意和第一种方法的区别）

<!-- 分页插件 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper</artifactId>
			<version>4.1.6</version>
		</dependency>
（2）在mybatis的配置文件中添加如下的插件

<!-- 配置mybatis的分页插件PageHelper -->
	     <plugins>
	         <!-- com.github.pagehelper为PageHelper类所在包名 -->
	         <plugin interceptor="com.github.pagehelper.PageHelper">
	             <!-- 设置数据库类型Oracle,Mysql,MariaDB,SQLite,Hsqldb,PostgreSQL六种数据库 -->
	             <property name="dialect" value="mysql"/>
	         </plugin>
	     </plugins>
（3）在controller或者service层进行使用插件

/**
     * 查询所有的person内容
          * @return
          */
        @RequestMapping(value = "/list")
        public String jumpJsp(Map<String, Object> result){
        PageHelper.startPage(1 , 5);
        List<Person> personList = personService.findPerson();
        //得到分页的结果对象
        PageInfo<Person> personPageInfo = new PageInfo<>(personList);
        //得到分页中的person条目对象
        List<Person> pageList = personPageInfo.getList();
        //将结果存入map进行传送
        result.put("pageInfo" , pageList);
        return "person_list";
        }
分析：对于这种方法的话，适用于对整合环境还是通过mybatis.xml的形式，所以，这种是具有针对性的一种情况。



方法三：使用最原始的方式（SpringBoot+Mybatis搭建中，Mybatis是利用在application.properties进行设置，并且mapper文件的操作还是使用.xml形式编写的sql语句，而非mapper注解编写）
使用步骤：

（1）在pom.xml文件中添加分页插件的依赖

<!-- 分页插件 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper</artifactId>
			<version>4.1.6</version>
		</dependency>
（2）添加下面一个类

package com.hnu.scw.config;

import com.github.pagehelper.PageHelper;
import org.apache.ibatis.plugin.Interceptor;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Properties;

/**
 * @ Author     ：scw
 * @ Date       ：Created in 下午 2:48 2018/6/17 0017
 * @ Description：用于配置分页插件的使用
 * @ Modified By：
 * @Version: $version$
 */
@Configuration
public class PgeHeplerConfig {
    //将分页插件注入到容器中
    @Bean
    public PageHelper pageHelper() {
        //分页插件
        PageHelper pageHelper = new PageHelper();
        Properties properties = new Properties();
        properties.setProperty("reasonable", "true");
        properties.setProperty("supportMethodsArguments", "true");
        properties.setProperty("returnPageInfo", "check");
        properties.setProperty("params", "count=countSql");
        pageHelper.setProperties(properties);

        //添加插件
        new SqlSessionFactoryBean().setPlugins(new Interceptor[]{pageHelper});
        return pageHelper;
    }

}
或者如下的代码：

package com.hnu.scw.config;
import com.github.pagehelper.PageHelper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Properties;

/**
 * @ Author     ：scw
 * @ Date       ：Created in 下午 2:48 2018/6/17 0017
 * @ Description：用于配置分页插件的使用
 * @ Modified By：
 * @Version: $version$
 */
@Configuration
public class PgeHeplerConfig {
    //将分页插件注入到容器中
    @Bean
    public PageHelper pageHelper() {
        //分页插件
        PageHelper pageHelper = new PageHelper();
        Properties properties = new Properties();
        properties.setProperty("reasonable", "true");
        properties.setProperty("supportMethodsArguments", "true");
        properties.setProperty("helperDialect", "mysql");
        properties.setProperty("params", "count=countSql");
        pageHelper.setProperties(properties);
        return pageHelper;
    }

}
还可以直接在springboot的启动类添加下面的代码即可。（其实都一样的道理，因为上面的类都是用了@Configuration注解配置了的，也就是添加到了容器中）

//将分页插件注入到容器中
    @Bean
    public PageHelper pageHelper() {
        //分页插件
        PageHelper pageHelper = new PageHelper();
        Properties properties = new Properties();
        properties.setProperty("reasonable", "true");
        properties.setProperty("supportMethodsArguments", "true");
        properties.setProperty("helperDialect", "mysql");
        properties.setProperty("params", "count=countSql");
        pageHelper.setProperties(properties);
        return pageHelper;
    }
（3）直接使用

/**
     * 查询所有的person内容
     * @return
     */
    @RequestMapping(value = "/list")
    public String jumpJsp(Map<String, Object> result){
        PageHelper.startPage(1 , 5);
        List<Person> personList = personService.findPerson();
        //得到分页的结果对象
        PageInfo<Person> personPageInfo = new PageInfo<>(personList);
        //得到分页中的person条目对象
        List<Person> pageList = personPageInfo.getList();
        //将结果存入map进行传送
        result.put("pageInfo" , pageList);
        return "person_list";
    }
情景二：Spring+SpringMVC+Mybatis+Maven环境
使用步骤：（其实这个就类似情景一种的方式二，换汤不换药）

（1）在pom.xml中添加分页插件的依赖

<!-- 分页插件 -->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>4.1.6</version>
    </dependency>
（2）在mybatis中的配置文件SqlMapConfig.xml（这个名字是你自己搭建环境所取的，就是配置一些关于Mybatis的配置文件）添加分页插件。

 <!-- 配置mybatis的分页插件PageHelper -->
    <plugins>
        <!-- com.github.pagehelper为PageHelper类所在包名 -->
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 设置数据库类型Oracle,Mysql,MariaDB,SQLite,Hsqldb,PostgreSQL六种数据库 -->
            <property name="dialect" value="mysql"/>
        </plugin>
    </plugins>
（3）在controller层或者service实现层中使用分页。

/**
     * 查询所有的person内容
     * @return
     */
    @RequestMapping(value = "/list")
    public String jumpJsp(Map<String, Object> result){
        PageHelper.startPage(1 , 8);
        List<Person> personList = personService.findPerson();
        //得到分页的结果对象
        PageInfo<Person> personPageInfo = new PageInfo<>(personList);
        //得到分页中的person条目对象
        List<Person> pageList = personPageInfo.getList();
        //将结果存入map进行传送
        result.put("pageInfo" , pageList);
        return "person_list";
    }
总结：
（1）pagehelper插件本身就是基于Mybatis这种框架进行开发的插件。所以，主要都是针对Mybatis数据操作的架构的。

（2）上面描述了多种情况的具体配置方式，都是自身经过实际开发编写的，而且对于不同的情景，各位要理解为什么要这样，这虽然只是讲解了分页插件的使用，当遇到其他插件的时候，都可以类似进行处理。

（3）如果是用到的SSH（Spring+SpringMVC+Hibernate）或者SpringBoot+Hibernate这样的架构的时候，这个插件是无法使用的，就需要自己通过hibernate的形式进行处理，比如可以用HQL语法或者Criteria进行处理即可。毕竟，Hibernate是一种全自动化的数据库操作框架。





