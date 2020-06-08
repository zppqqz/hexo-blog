设计模式-组合模式Composite

**一、前言**

**或者读者如果编写过前端的页面，肯定使用过等标签定义一些格式，然后格式之间互相组合，通过一种递归的方式组织成相应的结构，\**这种方式其实就是组合，将部分的组件镶嵌到整体之中\**；又或者文件和文件夹的组织关系，通过目录表项作为共同的特质（父类），一个文件夹可以包含多个文件夹和多个文件，一个文件容纳在一个文件夹之中。**



**首先整体的结构应该是一棵树，第二，所有的组件应该有一个共同的父类（有共同的本质），这个父类使得组件中的共同的本质可以提取出来（有了共同语言（父类）），进行互融，其实就是父类使用add方法，这样子类就可以通过抽象的方式通过父类来表达了**



//共同特质

```
3 public abstract class Entry {
 4     public abstract String getName();
 5     public abstract int getSize();
 6     public abstract void printList(String prefix);
 7     public  void printList(){
 8         printList("");
 9     }
10     public  Entry add(Entry entry) throws RuntimeException{
11         throw new RuntimeException();
12     }
13     public  String toString(){
14         return getName()+"<"+getSize()+">";
15     }
16 }
```

//叶子

```
public class File extends Entry {
 4 
 5     private String name;
 6     private int size;
 7     public File(String name,int size){
 8         this.name=name;
 9         this.size=size;
10     }
11     public String getName() {
12         return name;
13     }
14 
15     public int getSize() {
16         return size;
17     }
18 
19     public void printList(String prefix) {
20         System.out.println(prefix+"/"+this);
21     }
22 
23 }
```

```
Directory 类：可扩充节点（中间节点）
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.composite;
 2 
 3 import java.util.ArrayList;
 4 import java.util.Iterator;
 5 
 6 public class Directory extends Entry {
 7 
 8     String name;
 9     ArrayList entrys=new ArrayList();
10     public Directory(String name){
11         this.name=name;
12     }
13     public String getName() {
14         return name;
15     }
16 
17     public int getSize() {
18         int size=0;
19         Iterator it=entrys.iterator();
20         while(it.hasNext()){
21             size+=((Entry)it.next()).getSize();
22         }
23         return size;
24     }
25 
26     public Entry add(Entry entry) {
27         entrys.add(entry);
28         return this;
29     }
30     
31     public void printList(String prefix) {
32         System.out.println(prefix+"/"+this);
33         Iterator it=entrys.iterator();
34         Entry entry;
35         while(it.hasNext()){
36             entry=(Entry)it.next();
37             entry.printList(prefix+"/"+name);
38         }
39     }
40 
41 }
```

```
public class Main {

    public static void main(String[] args) {

        Directory root=new Directory("根目录");
        
        Directory life=new Directory("我的生活");
        File eat=new File("吃火锅",100);
        File sleep=new File("睡觉",100);
        File study=new File("学习",100);
        life.add(eat);
        life.add(sleep);
        life.add(study);
        
        Directory work=new Directory("我的工作");
        File write=new File("写博客",200);
        File paper=new File("写论文",200);
        File homework=new File("写家庭作业",200);
        work.add(write);
        work.add(paper);
        work.add(homework);
        
        Directory relax=new Directory("我的休闲");
        File music=new File("听听音乐",200);
        File walk=new File("出去转转",200);
        relax.add(music);
        relax.add(walk);
        
        Directory read=new Directory("我的阅读");
        File book=new File("学习书籍",200);
        File novel=new File("娱乐小说",200);
        read.add(book);
        read.add(novel);
        
        root.add(life);
        root.add(work);
        root.add(relax);
        root.add(read);

        root.printList("D:");
        System.out.println("=================");
        work.printList("work");
        System.out.println("=================");
        novel.printList("novel");
        

    }

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **运行结果：**

![img](https://images2018.cnblogs.com/blog/1157683/201806/1157683-20180628113723861-574674797.png)

**三、总结**

 **由此可见，我们以前使用的“\**容器+内容\**”，其实是通过组合模式实现的，组合模式保证了容器和内容的一致性，容器里面可以套容器，也可以放内容，但是内容已经是叶子结点了，不能继续扩充，还记得我们在抽象工厂方式中使用的模式之中，为了将零件组装成产品，我们就使用了组合模式，非常的有意思，通过递归来遍历所有的内容。组合模式在我们的生活中使用的非常普遍，我们一定要使用好这个模式，理解其中的抽象，特别是add()的定义，抽象类和实现类之间的参数传递，这点至关重要，当然我们又使用了模板方法和迭代器，希望大家能明白模式之间的联系以及相互使用的道理。**