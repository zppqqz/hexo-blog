设计模式-外观Facade  简单化 



**、前言**

 **外观模式是一种非常简单的模式，简单到我们经常都会使用，比如对于类A和B，如果两者需要交互，经过一定的处理过程才能实现某一个具体的功能，那么我们可以将这个处理的过程定义为一个新的类，然后在这个类里面将类A和B的处理步骤整合在一起，对于外界我们只暴露新的类中的这个接口，这样代码的复用性就非常的好了，可以将这些代码作为组件去让其他程序去使用，这在我们的开发之中是非常常见的。**

![img](https://images2018.cnblogs.com/blog/1157683/201806/1157683-20180630111227986-914428218.png)



```java
 7 public class PageMaker {
 8     private PageMaker(){
 9     }
10     public static void makePage(String mailAddr,String filename) throws IOException{
11         Properties pro=DataBase.getProperties("mailData");
12         String username=pro.getProperty(mailAddr);
13         HTMLWriter html=new HTMLWriter(new FileWriter(filename));
14         html.title("编程之美");
15         html.paragraph("欢迎 "+username+" 来到我的程序空间！");
16         html.paragraph("这里有你想要的一切~~");
17         html.mailTo(mailAddr, username);
18         html.close();
19         System.out.println("为"+username+"创建"+filename+"文件成功！");
20     }
21     
22 }
```

```
package zyr.dp.facade;
 2 
 3 import java.io.IOException;
 4 
 5 public class Main {
 6 
 7     public static void main(String[] args) {
 8         try {
 9             PageMaker.makePage("2655100328@qq.com", "my_html.html");
10         } catch (IOException e) {
11             e.printStackTrace();
12         }
13     }
14     
15 }
```

 **facade外观模式是一种非常常用的模式，特别是在组织一些复杂的相互调用的逻辑的时候，为外界提供统一的接口（API）,可以看到在设计模式中，最常用的应该就是模板方法和facade模式了，很多时候很多需求需要我们认真的取舍，人无远虑必有近忧，同样的，只有为以后的可复用性、可扩展性来考虑，我们的代码才是好的代码。**