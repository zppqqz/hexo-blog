实战-实现Aspect切面 



## 系统日志

1、新建一个Springboot工程
2、添加必要的依赖
AOP 必须

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
gson主要是我用于数据的处理，不是必须的

<dependency>
	<groupId>com.google.code.gson</groupId>
	<artifactId>gson</artifactId>
	<version>2.8.1</version>
</dependency>





<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<scope>runtime</scope>
</dependency>
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<optional>true</optional>
</dependency>



### 3、日志实体类和service

@Data
public class SysLogBO {

    private String className;
     
    private String methodName;
     
    private String params;
     
    private Long exeuTime;
     
    private String remark;
     
    private String createDate;
}


@Slf4j
@Service
public class SysLogService {

    public boolean save(SysLogBO sysLogBO){
        // 这里就不做具体实现了
        log.info(sysLogBO.getParams());
        return true;
    }
}


4、定义日志注解
这里呢，我们记录日志使用注解的形式。所以，先定义一个注解

package com.space.aspect.anno;

import java.lang.annotation.*;

/**
 * 定义系统日志注解
 * @author zhuzhe
 * @date 2018/6/4 9:24
 * @email 1529949535@qq.com
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface SysLog {
    String value() default "";
}

package com.space.aspect.aspect;

import com.google.gson.Gson;
import com.space.aspect.anno.SysLog;
import com.space.aspect.bo.SysLogBO;
import com.space.aspect.service.SysLogService;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

/**
 * 系统日志切面
 * @author zhuzhe
 * @date 2018/6/4 9:27
 * @email 1529949535@qq.com
 */
@Aspect  // 使用@Aspect注解声明一个切面
@Component
public class SysLogAspect {

    @Autowired
    private SysLogService sysLogService;

    /**
     * 这里我们使用注解的形式
     * 当然，我们也可以通过切点表达式直接指定需要拦截的package,需要拦截的class 以及 method
     * 切点表达式:   execution(...)
     */
    @Pointcut("@annotation(com.space.aspect.anno.SysLog)")
    public void logPointCut() {}

    /**
     * 环绕通知 @Around  ， 当然也可以使用 @Before (前置通知)  @After (后置通知)
     * @param point
     * @return
     * @throws Throwable
     */
    @Around("logPointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        long beginTime = System.currentTimeMillis();
        Object result = point.proceed();
        long time = System.currentTimeMillis() - beginTime;
        try {
            saveLog(point, time);
        } catch (Exception e) {
        }
        return result;
    }

    /**
     * 保存日志
    
     * @param joinPoint
    
     * @param time
     */
    private void saveLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        SysLogBO sysLogBO = new SysLogBO();
        sysLogBO.setExeuTime(time);
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        sysLogBO.setCreateDate(dateFormat.format(new Date()));
        SysLog sysLog = method.getAnnotation(SysLog.class);
        if(sysLog != null){
            //注解上的描述
            sysLogBO.setRemark(sysLog.value());
        }
        //请求的 类名、方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        sysLogBO.setClassName(className);
        sysLogBO.setMethodName(methodName);
        //请求的参数
        Object[] args = joinPoint.getArgs();
        try{
            List<String> list = new ArrayList<String>();
            for (Object o : args) {
                list.add(new Gson().toJson(o));
            }
            sysLogBO.setParams(list.toString());
        }catch (Exception e){ }
    sysLogService.save(sysLogBO);
}
}
6、测试
接下来，我们就来测试一下吧
 
 package com.space.aspect.controller;
  
  import com.space.aspect.anno.SysLog;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RequestParam;
  import org.springframework.web.bind.annotation.RestController;
  
  /**
   * @author zhuzhe
   * @date 2018/6/4 9:47
   * @email 1529949535@qq.com
   */
  @RestController
  public class TestController {
  
      @SysLog("测试")
      @GetMapping("/test")
      public String test(@RequestParam("name") String name){
          return name;
      }
  }
  启动项目，访问我们的test方法。
  ![image-20200520172353987](C:\Users\zpp\AppData\Roaming\Typora\typora-user-images\image-20200520172353987.png)