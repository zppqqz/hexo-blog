设计模式-观察者（Observer）模式

**，仲裁者模式是用来解除复杂对象之间的相互调用的关系，从而独立出来进行开发，而观察者模式是在被观察者状态改变的时候被动的被唤醒进行相应的处理，两者的实现比较类似，比如都是被动唤醒的，但是思想和用处是不一样的，被唤醒之后的处理是不一样的。**

```
public abstract class NumberGenerator {
 7 
 8     private ArrayList observers=new ArrayList();
 9     
10     public void add(Observer observer){
11         observers.add(observer);
12     }
13     public void remove(Observer observer){
14         observers.remove(observer);
15     }
16     public void notifyObserver(){
17         Iterator it=observers.iterator();
18         while(it.hasNext()){
19             Observer object=(Observer)it.next();
20             object.update(this);
21         }
22     }
23     public abstract void execuate();
24     public abstract int getNumber();
25 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
RandomNumberGenerator 类：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.observer;
 2 
 3 import java.util.Random;
 4 
 5 public class RandomNumberGenerator extends NumberGenerator {
 6 
 7     private Random random=new Random();
 8     private int number;
 9     public int getNumber(){
10         return number;
11     }
12     public void execuate() {
13         for(int i=0;i<20;i++){
14             number=random.nextInt(60);
15             notifyObserver();
16         }
17     }
18 
19 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **Observer接口：**

```
1 package zyr.dp.observer;
2 
3 public interface Observer {
4     public abstract void update(NumberGenerator object);
5 }
```

 **DigitalObserver类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.observer;
 2 
 3 public class DigitalObserver implements Observer {
 4 
 5     public void update(NumberGenerator object) {
 6         System.out.println("DigitalObserver:"+object.getNumber());
 7         try {
 8             Thread.sleep(100);
 9         } catch (InterruptedException e) {
10             e.printStackTrace();
11         }
12     }
13 
14 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **GraphObserver类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.observer;
 2 
 3 public class GraphObserver implements Observer {
 4 
 5     public void update(NumberGenerator object) {
 6         System.out.print("GraphObserver:");
 7         for(int i=0;i<object.getNumber();i++){
 8             System.out.print("*");
 9             try {
10                 Thread.sleep(100);
11             } catch (InterruptedException e) {
12                 e.printStackTrace();
13             }
14         }
15         System.out.println();
16     }
17 
18 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **Main类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.observer;
 2 
 3 public class Main {
 4 
 5     public static void main(String[] args) {
 6         NumberGenerator numberGenerator=new RandomNumberGenerator();
 7         numberGenerator.add(new DigitalObserver());
 8         numberGenerator.add(new GraphObserver());
 9 
10         numberGenerator.execuate();
11     }
12 
13 }
```

 **由此可以看到当被观察者的状态发生改变的时候会主动通知观察者，使用notifyObserver的方法将自己的状态传递过去，因为是自己定义的被观察者的抽象类以及接口，因此使用起来非常的方便。代码也不是很多，能够按照自己的要求来完成更新操作，对比于仲裁者模式，被观察者是主动将自己的内容传递给观察者的，而仲裁者模式中，组员是本身就已经组合（委托）进了仲裁者之中，这也是一点不同。代码比较简单，这里被观察者使用了抽象类而不使用接口的原因是需要定义对观察者对象的委托，因此使用了抽象类，而观察者只用了update方法将被观察者通过参数传递的方式委托进来，因此使用接口更加清晰一点，当然抽象类也可以，只不过能使用接口的就不要使用抽象类，因为一个类只能继承一个父类，但是可以实现很多接口。**

 **2.2、使用java自带的观察者模式**

```
RandomNumberGenerator 类：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.java;
 2 
 3 import java.util.Observable;
 4 import java.util.Random;
 5 
 6 public class RandomNumberGenerator extends Observable {
 7 
 8     private Random random=new Random();
 9     private int number;
10     public int getNumber(){
11         return number;
12     }
13     public void execuate() {
14         for(int i=0;i<20;i++){
15             number=random.nextInt(60);
16             setChanged();
17             notifyObservers();
18         }
19     }
20 
21 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **DigitalObserver类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.java;
 2 
 3 import java.util.Observable;
 4 import java.util.Observer;
 5 
 6 public class DigitalObserver implements Observer {
 7 
 8     public void update(Observable object, Object arg) {
 9         System.out.println("DigitalObserver为："+((RandomNumberGenerator)object).getNumber());
10         try {
11             Thread.sleep(100);
12         } catch (InterruptedException e) {
13             e.printStackTrace();
14         }
15     }
16 
17 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
GraphObserver 类：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.java;
 2 
 3 import java.util.Observable;
 4 import java.util.Observer;
 5 
 6 public class GraphObserver implements Observer {
 7 
 8 
 9     public void update(Observable object, Object arg) {
10         System.out.print("GraphObserver为:");
11         for(int i=0;i<((RandomNumberGenerator)object).getNumber();i++){
12             System.out.print("*");
13             try {
14                 Thread.sleep(100);
15             } catch (InterruptedException e) {
16                 e.printStackTrace();
17             }
18         }
19         System.out.println();
20     }
21 
22 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **Main类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.java;
 2 
 3 import java.util.Observable;
 4 
 5 public class Main {
 6 
 7     public static void main(String[] args) {
 8         Observable observable=new RandomNumberGenerator();
 9         observable.addObserver(new DigitalObserver());
10         observable.addObserver(new GraphObserver());
11 
12         ((RandomNumberGenerator)observable).execuate();
13     }
14 
15 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  **可以看到在java自定义的观察者模式之中，首先要修改 setChanged();来使得notifyObservers生效，其次，传递的参数不是很灵活，需要强制转换成我们想要的东西，最后在使用的时候也需要强制转换，这是比较麻烦的，并且被观察者也是继承了抽象类Observable，不方便以后功能的扩展，\**如果以后再想继承其它的类就很困难了\**。我们自己设计的时候，可以使用某些方式把抽象类变成接口，不过也需要一定的操作。**

 **三、总结**

   **通过观察者模式使得观察者和被观察者之间面向抽象编程，观察者不用知道自己观察的对象到底是谁的实例，只需要知道这个对象继承了被观察者的抽象类，因此当被观察者增加的时候，观察者可以不用修改。同样的，对于被观察者的实例来说，并不需要知道自己到底是被哪一个观察者观察了，只需要知道观察自己的观察者肯定使用了观察者的接口，因此观察者和被观察者之间通过面向抽象编程提高了可扩展性，便于组件化。**

  **我们可以看到在面向对象编程中能够使用委托（组合）的就不要使用继承，委托是弱关联，继承是强关联。并且将一些共同操作抽象出来放到抽象类之中去定义，在参数传递中不使用具体类型而是用接口或者抽象类，这样的设计思想便于组件化，具有可替换性。**