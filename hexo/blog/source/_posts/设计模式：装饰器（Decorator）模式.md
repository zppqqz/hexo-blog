设计模式-装饰器模式 

## **设计模式：装饰器（Decorator）模式**

**一、前言**

  **装饰器模式也是一种非常重要的模式，在Java以及程序设计中占据着重要的地位。比如Java的数据流处理，我们可能看到数据流经过不同的类的包装和包裹，最终形成了我们需要的流，比如说从二进制到字节流再到字符流，这中间其实就是经过了装饰器的处理，在不改变原来的数据的基础上，加入属于自己的特征**

**Display 类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.decorator;
 2 
 3 public abstract class Display {
 4     
 5     public abstract int getColunmns();
 6     public abstract int getRows();
 7     public abstract String getTowText(int rowID);
 8     public  void  show(){
 9         for(int i=0;i<getRows();i++){
10             System.out.println(getTowText(i));
11         }
12     }
13 
14 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**StringDisplay类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.decorator;
 2 
 3 public class StringDisplay extends Display {
 4 
 5     String name;
 6     
 7     public StringDisplay(String name){
 8         this.name=name;
 9     }
10     
11     public int getColunmns() {
12         return name.getBytes().length;
13     }
14     
15     public int getRows() {
16         return 1;
17     }
18 
19     public String getTowText(int rowID) {
20         if(rowID==0){
21             return name;
22         }else{
23             return null;
24         }
25     }
26 
27 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**Border类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 package zyr.dp.decorator;
2 
3 public abstract class Border extends Display {
4 
5     protected Display display;
6     public Border(Display display){
7         this.display=display;
8     }
9 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**SideBorder类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.decorator;
 2 
 3 public class SideBorder extends Border {
 4 
 5     String ch;
 6     protected SideBorder(Display display,String ch) {
 7         super(display);
 8         this.ch=ch;
 9     }
10 
11     public int getColunmns() {
12         return display.getColunmns()+2;
13     }
14 
15     public int getRows() {
16         return display.getRows();
17     }
18 
19     public String getTowText(int rowID) {
20         return ch+display.getTowText(rowID)+ch;
21     }
22 
23 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **FullBorder类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.decorator;
 2 
 3 public class FullBorder extends Border {
 4 
 5     public  FullBorder(Display display) {
 6         super(display);
 7     }
 8 
 9     public int getColunmns() {
10         return display.getColunmns()+2;
11     }
12 
13     public int getRows() {
14         return display.getRows()+2;
15     }
16 
17     public String getTowText(int rowID) {
18         if(rowID==0){
19             return "+"+makeLine("-",display.getColunmns())+"+";
20         }else if(rowID==display.getRows()+1){
21             return "+"+makeLine("-",display.getColunmns())+"+";
22         }else{
23             return "|"+display.getTowText(rowID-1)+"|";
24         }
25     }
26     private String makeLine(String ch,int count){
27         StringBuffer sb=new StringBuffer();
28         for(int i=0;i<count;i++){
29             sb.append(ch);
30         }
31         return sb.toString();
32     }
33 
34 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**Main类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.decorator;
 2 
 3 public class Main {
 4 
 5     public static void main(String[] args) {
 6 
 7         Display d1=new StringDisplay("朱彦荣");
 8         d1.show();
 9         System.out.println("\n");
10         Display d2=new SideBorder(d1,"*");
11         d2.show();
12         System.out.println("\n");
13         Display d3=new FullBorder(d2);
14         d3.show();
15         System.out.println("\n");
16         Display d4=new SideBorder(
17                      new FullBorder(
18                        new FullBorder(
19                           new SideBorder(
20                                new FullBorder(
21                                        new StringDisplay("李山秀")
22                                ),
23                                "#"
24                           )
25                         )
26                      ),
27                      "*"
28                     );
29         d4.show();
30     }
31 
32 }
```

![image-20200601144255496](C:\Users\zpp\AppData\Roaming\Typora\typora-user-images\image-20200601144255496.png)