设计模式-Mediator  简单化



## 设计模式：仲裁者（Mediator）模式

**一、前言**

  **Mediator模式又称为仲裁者模式或者中介者模式，所起的作用是仲裁和中介，帮助其它类之间进行交流。在仲裁者模式之中，我们要明确两个概念，那就是仲裁者（Mediator）和\**组员（Colleague）\**，不管组员有什么事情，都会向仲裁者汇报，仲裁者会根据全局的实际情况向其他Colleague作出指示，共同完成一定的\**逻辑\**功能。** 

**Mediator接口：（仲裁者接口）**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 package zyr.dp.mediator;
2 
3 public interface Mediator {
4     public abstract void createColleagues();
5     public abstract void ColleagueChanged();
6 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **Colleague 组员接口：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 package zyr.dp.mediator;
2 
3 public interface Colleague {
4     public abstract void setMediator(Mediator mediator);
5     public abstract void setColleagueEnable(boolean enable);
6 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **ColleagueButton实现类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.mediator;
 2 
 3 import java.awt.Button;
 4 
 5 public class ColleagueButton extends Button implements Colleague {
 6 
 7     private static final long serialVersionUID = 1309335015058337931L;
 8     
 9     public ColleagueButton(String caption){
10         super(caption);
11     }
12     
13     private Mediator mediator;
14     public void setMediator(Mediator mediator) {
15         this.mediator=mediator;
16     }
17 
18     public void setColleagueEnable(boolean enable) {
19           setEnabled(enable);
20     }
21 
22 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **ColleagueCheckBox实现类：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.mediator;
 2 
 3 import java.awt.Checkbox;
 4 import java.awt.CheckboxGroup;
 5 import java.awt.event.ItemEvent;
 6 import java.awt.event.ItemListener;
 7 
 8 public class ColleagueCheckBox extends Checkbox implements ItemListener,Colleague {
 9 
10     private static final long serialVersionUID = -628547709974079324L;
11 
12     public ColleagueCheckBox(String caption,CheckboxGroup g,boolean b){
13         super(caption,g,b);
14     }
15     
16     private Mediator mediator;
17     public void setMediator(Mediator mediator) {
18         this.mediator=mediator;
19     }
20 
21     public void setColleagueEnable(boolean enable) {
22         setEnabled(enable);
23 
24     }
25 
26     public void itemStateChanged(ItemEvent e) {
27         mediator.ColleagueChanged();
28     }
29 
30 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
ColleagueTextField 实现类：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 package zyr.dp.mediator;
 2 
 3 import java.awt.Color;
 4 import java.awt.TextField;
 5 import java.awt.event.TextEvent;
 6 import java.awt.event.TextListener;
 7 
 8 public class ColleagueTextField extends TextField implements TextListener, Colleague {
 9 
10     private static final long serialVersionUID = 8539124564669846422L;
11     
12     public ColleagueTextField(String text,int columns){
13         super(text,columns);
14     }
15     
16     private Mediator mediator;
17     public void setMediator(Mediator mediator) {
18         this.mediator=mediator;
19     }
20 
21     public void setColleagueEnable(boolean enable) {
22         setEnabled(enable);
23         setBackground( enable? Color.WHITE : Color.BLACK );
24     }
25 
26     public void textValueChanged(TextEvent e) {
27         mediator.ColleagueChanged();
28     }
29 
30 
31 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
LoginFrame 实现类：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
  1 package zyr.dp.mediator;
  2 
  3 import java.awt.CheckboxGroup;
  4 import java.awt.Color;
  5 import java.awt.Frame;
  6 import java.awt.GridLayout;
  7 import java.awt.Label;
  8 import java.awt.event.ActionEvent;
  9 import java.awt.event.ActionListener;
 10 
 11 public class LoginFrame extends Frame implements ActionListener , Mediator {
 12 
 13     private static final long serialVersionUID = -509490729876024682L;
 14 
 15     private ColleagueButton buttonOK;
 16     private ColleagueButton buttonCancel;
 17     
 18     private ColleagueCheckBox chkGuest;
 19     private ColleagueCheckBox chkUser;
 20     
 21     private ColleagueTextField tfUser;
 22     private ColleagueTextField tfPass;
 23     
 24     public LoginFrame(String title){
 25         super(title);
 26         setBackground(Color.lightGray);
 27         setLayout(new GridLayout(4,2));
 28         createColleagues();
 29         add(chkGuest);
 30         add(chkUser);
 31         add(new Label("用户名："));
 32         add(tfUser);
 33         add(new Label("密码："));
 34         add(tfPass);
 35         add(buttonOK);
 36         add(buttonCancel);
 37         ColleagueChanged();
 38         pack();
 39         show();
 40     }
 41     
 42     public void createColleagues() {
 43         CheckboxGroup chk=new CheckboxGroup();
 44         chkGuest=new ColleagueCheckBox("Guest",chk,true);
 45         chkUser=new ColleagueCheckBox("User",chk,false);
 46         buttonOK=new ColleagueButton("OK");
 47         buttonCancel=new ColleagueButton("Cancel");
 48         tfUser=new ColleagueTextField("",10);
 49         tfPass=new ColleagueTextField("",10);
 50         tfPass.setEchoChar('#');
 51         
 52         chkGuest.setMediator(this);
 53         chkUser.setMediator(this);
 54         buttonOK.setMediator(this);
 55         buttonCancel.setMediator(this);
 56         tfUser.setMediator(this);
 57         tfPass.setMediator(this);
 58         
 59         chkGuest.addItemListener(chkGuest);
 60         chkUser.addItemListener(chkUser);
 61         buttonOK.addActionListener(this);
 62         buttonCancel.addActionListener(this);
 63         tfUser.addTextListener(tfUser);
 64         tfPass.addTextListener(tfPass);
 65 
 66     }
 67 
 68     public void ColleagueChanged() {
 69 
 70         if(chkGuest.getState()){
 71             tfUser.setColleagueEnable(false);
 72             tfPass.setColleagueEnable(false);
 73             buttonOK.setColleagueEnable(true);
 74         }else{
 75             tfUser.setColleagueEnable(true);
 76             userPassChanged();
 77         }
 78     }
 79 
 80     private void userPassChanged() {
 81         if(tfUser.getText().length()>0){
 82             tfPass.setColleagueEnable(true);
 83             if(tfPass.getText().length()>0){
 84                 buttonOK.setColleagueEnable(true);
 85             }else{
 86                 buttonOK.setColleagueEnable(false);
 87             }
 88         }else{
 89             tfPass.setColleagueEnable(false);
 90             buttonOK.setColleagueEnable(false);
 91         }
 92         
 93     }
 94 
 95     public void actionPerformed(ActionEvent e) {
 96         System.out.println(e.toString());
 97         System.exit(0);
 98     }
 99 
100 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
Main 方法：
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 package zyr.dp.mediator;
2 
3 public class Main {
4 
5     public static void main(String[] args) {
6         LoginFrame login=new LoginFrame("仲裁者模式");
7     }
8 
9 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**是单选框都有两个，来自于同一个类，我们要怎么识别呢，可能还要通过一定的方法得到单选框的ID，这是极其复杂的，最让人难以接受的是，如果需求发生了改变，比如又增加了一个控件，剩余的所有组件的代码都要修改，而这些代码之间本就混乱无比，再次修改就更让人难以接受了，因此这不是一个好的办法，甚至可以说是一个非常差的解决办法。**

  **那么有什么好的解决办法吗？那就是将这些复杂的逻辑操作独立出来，新创建一个类，在每个控件的状态发生改变的时候就能通过某种方式通知到这个类，让这个类改变这些控件的状态，这样说来这个新的类应该持有这些控件的引用，这样才能委托这些控件去改变自己的状态，同时这些控件要想通知这个新的类，那就要持有这个类（或其父类）的引用，这样才能委托这个新的类去实现所有状态的改变。这样我们的思路就清晰明了了，如果一个再增加一个新的控件，需要添加逻辑，我们只需要修改仲裁者的方法，就可以做到全局的掌控，达到了修改最少的代码来扩展程序的行为，因此仲裁者模式非常的重要和实用。**

**，仲裁者模式适合于某一个部分发生改变就会导致其他部分做出相应的改变的情况，我们将这种变换的规律抽象出来，变成仲裁者，从而很好的协商各个部分，当组员有问题的时候（状态发生改变），直接告诉仲裁者，不与其他组员打交道，让仲裁者负责这些繁琐的事务，这样条理就很清晰了，因此仲裁者也成为中介者，可以想象很多人要去租房，很多人要把房子出租，如果私下里面去商量，既浪费时间，又很难找到对方，通过中介这个对象，它收集了很多的租房和出租房的信息，这样就能很快的找到最适合的房子。由此可见生活就是最好的设计模式。仲裁者模式对于代码的修改（很容易定位错误）、新的成员的加入、代码的复用（组员部分可以复用，仲裁者部分不易复用）都有着一定的简化，将该集中处理的集中起来，将该分散的分散出去，无疑是一种好的设计模式。对比于外观模式Facade，仲裁者需要和组员沟通，是双向的，而外观模式facade角色只是对其他角色进行整合，是单向的。设计模式至今我们已经研究了16种了，这其中很多设计模式都是有着关联的，同样也有着不同之处，但是都和实际问题是分不开的，设计模式是实际问题的抽象和解决方法，为了提高可扩展性和组件化，实际问题是设计模式的来源和最终归宿。**