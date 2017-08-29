# 1.  创建型模式

## 1.1.  简单工厂Factory

简单工厂模式不属于23种设计模式

### 1.1.1.  普通工厂

创建工厂类，一个创建对象的方法，根据参数类型不同，返回子类对象，如：邮件和短信

```
factory.getProduct(int type){}
```

### 1.1.2.  多方法工厂

不同方法返回不同子类对象

```
factory.getProductA(){}
factory.getProductB(){}
```

### 1.1.3.  静态工厂

方法是静态的，不需要实例工厂，最常用

```
Factory.getProduct(int type){}

Factory.getProduct()

Factory.getProductA(){}
Factory.getProductB(){}
```



## 1.2.  工厂方法FactoryMethod

简单工厂模式有一个问题就是，类的创建依赖工厂类，也就是说，如果想要拓展程序，必须对工厂类进行修改，这违背了闭包原则。

工厂方法模式中有多个工厂类（每个对象一个工厂，工厂实现同一接口）

## 1.3.  抽象工厂AbstractFactory

抽象工厂是应对产品族概念的。比如说，每个汽车公司可能要同时生产轿车，货车，客车，那么每一个工厂都要有创建轿车，货车和客车的方法。又比如每个工厂可能同时生成鼠标和键盘。

与工厂方法类似，不同的是每个工厂类可以生产不同类别的产品，如即可以生产鼠标也可以生成键盘。

与工厂方法的区别

- 工厂方法只能生产一种产品类，抽象工厂方法能生产不同产品族的多个产品类(多个抽象产品类)
- 在只有一个产品族的情况下，抽象工厂模式实际上退化到工厂方法模式。

## 1.4.  单例Singleton

l  私有静态实例，

l  私有构造方法， 

l  synchronized getInstance()(每次调用都加锁，性能下降，可以使用静态内部类new出对象)

## 1.5.  建造者Builder

创建复合对象，多部件制造和组装分开，构建与表示分离，同样的构建过程可以创建不同的表示，如：生成电脑包括生产CPU，显示器，内存，不同厂商生产出的不一样。

角色：

- 建造者（包含建造各部件的方法）、
- 产品、
- 指挥者（指挥者类Director，其拥有一个建造者对象和建造PC产品的方法construct（组装产品），该方法通过具体建造者对象，依次执行每个步骤，最后返回建造完成的产品对象）

建造者模式与工厂模式区别

建造者模式可以说是对工厂模式的扩展，工厂类提供了生产单个产品的功能，而建造者模式则可以将各种产品集中起来进行统一管理。工厂模式关注的是整个产品，建造者模式关注的是产品各**组成部分的创建**过程。

建造者模式与模板模式区别

建造者模式是创建型模式，模板模式是行为型模式，建造者模式使用组合，模板模式使用继承，建造者模式中的指挥者生产产品的方法construct类似模板模式的算法步骤

## 1.6.  原型Prototype

分2种：

- 普通原型模式：实现Cloneable接口及**clone**方法，
- 带原型管理器：除实现clone方法外，增加原型管理器角色，该角色保持一个聚集（Map），作为对所有原型对象的登记，这个角色提供必要的方法，供外界增加新的原型对象和取得已经登记过的原型对象

如果需要创建的原型对象数目较少而且比较固定的话，可以采取普通原型模式，如果要创建的原型对象数目不固定的话，采用原型管理器

使用场景：当直接创建对象的代价比较大时，则采用这种模式。例如，一个对象需要在一个高代价的数据库操作之后被创建。

# 2.  结构型模式

## 2.1.  适配器Adapter

将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不**兼容**而不能一起工作的那些类可以在一起工作。

### 2.1.1.  类适配

继承旧类+实现新接口

### 2.1.2.  对象适配

持有旧类+实现新接口 

### 2.1.3.  接口适配（缺省适配模式）

**适配器模式的用意**是要改变源的接口，以便于目标接口相容。**缺省适配的用意稍有不同**，它是为了方便建立一个不平庸的适配器类而提供的一种平庸实现。

方法：创建抽象类实现接口所有方法（适配器类），然后继承抽象类，实现部分方法。

适用于当不希望实现一个接口中所有的方法，例如鲁智深是和尚，但只习武。

```
interface 和尚{吃斋，念佛，习武}，

Abstract 天星{吃斋，念佛，习武}，

鲁智 extend 天星{习武}
```



## 2.2.  装饰Decorator

实现**同一接口**,持有原对象的引用（通过传参的方式），调用原对象同名方法内前后**增加功能**。

例如：Spring 切面 在所有方法被调用时，记录日志。

## 2.3.  代理Proxy

类似装饰模式，代理持有且实例化原对象（比如：在代理构造方法中new原对象）

代理模式的目的是：使用代理对象来**控制**对原有对象的访问

## 2.4.  外观Facade

外观模式不涉及接口，解决类与类之间依赖关系，将依赖关系放到一个外观类中，类似spring，将类之间的关系写在配置文件中，如：电脑启动（CPU 内存硬盘），电脑是外观类，持有并实例化其他类。

外观模式为系统提供一个**统一的调用接口**，使子系统更加容易使用。

## 2.5.  桥接Bridge

桥（多维的其中一维抽象）持有原始类的接口（另外一维）的引用，通过调用桥的方法，调用原始类，桥接模式可以使多维度中各维度变化分离，使用组合将多维度联系起来，如：车（轿车，公交车）、路（公路，高速公路）；同一个游戏中不同型号的对象，在不同平台（PC，手机）画面的展现。适用于**多维组合解耦**。

```
AbstractRoad{ 
	Vehicle v  
	run(){
		v.run()
	}
}
Highway extend AbstractRoad{}
Freeway extend AbstractRoad{}

interface Vehicle{ run()}
Car impl Vehicle{run()}
Bus impl Vehicle{run()} 
```

与适配器模式区别：

适配器模式：改变已有的两个接口，使相容。先有两边的东西，再有适配器

桥模式：分离抽象与实现，解决多维的变化问题。先有桥，再有两边的东西。

## 2.6.  组合Composite

又称部分-整体模式，处理树形结构问题，将多个对象组合在一起进行操作，对象持有自己的数组（子节点）。

使得用户对单个对象和组合对象的使用具有一致性（同一结构）

样例：文件系统：文件+目录

## 2.7.  享元Flyweight

实现对象的共享，即共享池，实现面向大量对象请求时，对象复用，如：数据库连接池，可以减少内存的开销，通常与工厂模式一起使用，池持有对象的数组，当一个客户端请求时，工厂需要检查当前对象池中是否有符合条件的对象。

享元对象包含共享的内部状态，又可以通过方法传参的方式传入外部状态。

享元工厂负责创建和管理享元对象，持有享元的集合

```
FlyweightImpl{
	String name;//共享的内部状态,
	FlyweightImpl(String name){      
	}
	setAge(int age){//传入外部状态
	}
}
```

使用享元模式的条件： 

1. 系统中有大量的对象，他们使系统的效率降低。 
2. 这些对象的状态可以分离出所需要的内外两部分。

例子：String类的设计就是享元模式，当两个String 对象所包含的内容相同的时候，JVM 只创建一个String 对象对应这两个不同的对象引用。

# 3.  行为型模式

## 3.1.  父与子

### 3.1.1.  策略Strategy(表驱动模式)

多用在算法决策系统，对算法封装，用户决定使用哪种算法，如：加减乘除实现同一接口、复杂的if else分支，可以把各种情况定义为字典-实现的映射，根据输入的字典，决定使用哪种算法。

context:环境角色持有一个策略的引用

```
Content{
  Strategy stgy;
  Content(Strategy stgy){
    this.stgy=stgy;
  }
  todo(){
    stgy.todo();
  }
}
```

### 3.1.2.  模板方法Template methed

抽象类定义算法结构，子类实现算法步骤

```
AbstractClass{
  abstract step1();
  abstract step2();
  abstract step3();
  templateMethed(){
   step1();
   step2();
   step3(); 
  }
}
```

## 3.2.  两类之间

### 3.2.1.  观察者Observer

对象持有观察者数组，可以添加删除观察者，当对象变化时，可以遍历数组，调用观察者的方法

### 3.2.2.  迭代器Iterator

迭代器模式是与集合共生共死的，迭代器对集合对象进行遍历操作，迭代器持有集合对象,集合对象提供返回迭代器的方法（return new 迭代器(this集合对象)），或迭代器是集合的内部成员类，可以直接访问集合对象数据

```
interface Iterator {
     public boolean hasNext();
     public Object next();
}

ConcreteIterator implements Iterator {
     private List list = null;
     private int index; 
     public ConcreteIterator(List list) {//持有集合对象
         super();
         this.list = list;
     }
      public boolean hasNext(){
      	return index < list.getSize()
      }
     public Object next(){
       		Object object = list.get(index);
         index++;
         return object;
     }
} 

 ArrayList impl List {
      public void add(Object obj);  
      public Object get(int index);
      public Iterator iterator(){//返回迭代器
      		return new ConcreteIterator(this);
      } 
      public int getSize();
10 }
```



### 3.2.3.  责任链Chain of responsibility

使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系。处理者持有下一处理者的引用形成链，请求在这个链上传递，直到某一对象决定处理该请求。但是发出者并不清楚到底最终那个对象会处理该请求，责任链模式可以实现，在隐瞒客户端的情况下，对系统进行动态的调整。

- 链表中每一个对象都可能而且可以成为入口，而不是必须从链头开始。

- 如果是if else的分支，通常使用策略模式（表驱动模式）

- 如果处理者有上下级关系时，可以考虑使用责任链，如：打牌出牌，流程审批，java异常机制catch顺序，Struts2连接器，tomcat的Filter

```
Handler{
  Handler nextHandler //持有下一处理者的引用
  handleRequest(){
  	//todo or not todo something
    nextHandler.handleRequest();
  }
}
```

### 3.2.4.  命令Command

将一个请求封装成一个对象，实现对命令请求者（Invoker）和命令实现者（Receiver）的解耦。

调用者->命令->执行者，调用者持有命令的引用，调用命令执行方法，命令持有执行者的引用，命令执行方法调用执行者的执行方法，例如：司令-->命令-->兵

 使用场景：

- 需要记录命令的日志
- 请求者和实现者解耦，不交互

## 3.3.  类状态

### 3.3.1.  备忘录Memento

用于备份恢复对象原先的状态，对象有创建备忘录，恢复备忘录的方法，备忘录包含需记录的对象的属性（多属性用Map）      ，备忘录管理类持有备忘录的引用（多备忘录用MAP），提供和保存备忘录，样例：JDBC事务回滚，Ctrl+Z

```
Object{
state
createMemento(){
 		return new Memento(this.state);  
	}
restoreMemento(Memento memento){
 		this.state=memento.getState();
	}
}

class Memento {  
    private String state = "";  
    public Memento(String state){  
        this.state = state;  
    }  
    public String getState() {  
        return state;  
    }  
    public void setState(String state) {  
        this.state = state;  
    }  
}  
MemoMgr{
    private Memento memento;  
    public Memento getMemento(){  
        return memento;  
    }  
    public void setMemento(Memento memento){  
        this.memento = memento;  
    }
}
```

### 3.3.2.  状态State

当一个对象有多个状态切换时，可以把状态抽象成类，不同状态执行不同操作。

- 状态类：状态名+行为，
- 环境类：定义客户感兴趣的接口。维护一个State子类的实例，这个实例定义当前状态。


环境类和状态类之间存在一种双向的关联关系，

谁定义状态转换？

1. 如果下一个状态跟当前状态有关，各个具体的状态类对象负责状态的转换，

2. 如果下一个状态跟当前状态无关，就可以选择由上下文来决定状态转换


例子：门开关状态，酒店房间状态

## 3.4.  中间类

### 3.4.1.  访问者Visitor

可以在不改变对象结构的情况下给对象增加新的操作（即访问者的方法）。被访问的元素有accept方法，以访问者为参数，调用访问者的访问方法，访问者的访问方法以被访问对象为参数，双重分派（一个方法根据两个宗量（方法接收者和入参）的类型来决定执行不同的代码）

```
Element：accept(Visitor v){  
	v.visit(this);  
}

Visitor: visit(Elemente){
	dosth  
}
```

即在不改变Element的情况下，为Element增加了dosth的操作。

访客到你家，获得你的引用，do sth。

### 3.4.2.  中介者Mediator

用于多个类之间相互交互，网状变星状，中介持有所有对象的引用，对象持有中介的引用，中介者使各个对象不需要显示地相互引用，从而使其耦合性松散，而且可以独立地改变他们之间的交互。

```
Mediator{ 
	ColleagueA a;
	ColleagueBb;
	AaffectB();
	BaffectA();
}

ColleagueA
	Mediator m
	dosth(){
		m. AaffectB();
	}
}
```

缺点：中介类易变上帝类

与Façade区别

- Façade为一组内聚的对象，提供一个统一的外部接口，

- Mediator为在一组互相协作的对象内部起协调作用。


样例：QQ群

### 3.4.3.  解释器Interpreter

对算法进行最小化拆分，用于编译器开发，解释器模式根据问题将算法和参与运算的值组合成一个树型结构，非叶子节点（NonterminalExpression）为算法，叶子节点（TerminalExpression）为参与运算的值，最后通过遍历这颗树求得问题的解。

# 适配器、装饰、代理模式区别

**适配器的特点在于兼容**，实现目标接口

**装饰器模式特点在于增强**，实现同一接口，持有接口类对象的引用

**代理模式的特点在于隔离**，实现同一接口，持有接口子类实例对象的引用

 

其实还有两类：并发型模式和线程池模式。

 

生产者消费者模式

 某个模块负责产生数据，这些数据由另一个模块来负责处理（此处的模块是广义的，可以是类、函数、线程、进程等）。产生数据的模块，就形象地称为生产者；而处理数据的模块，就称为消费者。在生产者与消费者之间在加个缓冲区，我们形象的称之为仓库（或经销商），生产者负责往仓库里进商品，而消费者负责从仓库里拿商品，这就构成了生产者消费者模式。 

 

**BlockingQueue**

** **

**ArrayBlockingQueue**

**LinkedBlockingQueue**

**DelayQueue****：**不会阻塞数据生产者****

**PriorityBlockingQueue****：**不会阻塞数据生产者

**SynchronousQueue****：**无缓冲