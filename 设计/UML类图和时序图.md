### 看懂UML类图和时序图

这里不会将UML的各种元素都提到，我只想讲讲类图中各个类之间的关系； 能看懂类图中各个类之间的线条、箭头代表什么意思后，也就足够应对 日常的工作和交流； 同时，我们应该能将类图所表达的含义和最终的代码对应起来； 有了这些知识，看后面章节的设计模式结构图就没有什么问题了；

本章所有图形使用Enterprise Architect 9.2来画,所有示例详见根目录下的design_patterns.EAP

#### 从一个示例开始

请看以下这个类图，类之间的关系是我们需要关注的：
![uml_class_struct](media/uml_class_struct.jpg)

车的类图结构为<<abstract>>，表示车是一个抽象类；
它有两个继承类：小汽车和自行车；它们之间的关系为实现关系，使用带空心箭头的虚线表示；
小汽车为与SUV之间也是继承关系，它们之间的关系为泛化关系，使用带空心箭头的实线表示；
小汽车与发动机之间是组合关系，使用带实心箭头的实线表示；
学生与班级之间是聚合关系，使用带空心箭头的实线表示；
学生与身份证之间为关联关系，使用一根实线表示；
学生上学需要用到自行车，与自行车是一种依赖关系，使用带箭头的虚线表示；
下面我们将介绍这六种关系；

#### 类之间的关系

##### 泛化关系(generalization)

类的继承结构表现在UML中为：泛化(generalize)与实现(realize)：

继承关系为 is-a的关系；两个对象之间如果可以用 is-a 来表示，就是继承关系：（..是..)

eg：自行车是车、猫是动物

泛化关系用一条带空心箭头的直接表示；如下图表示（A继承自B）；

![uml_generalization](media/uml_generalization.jpg)

eg：汽车在现实中有实现，可用汽车定义具体的对象；汽车与SUV之间为泛化关系；

![uml_generalize](media/uml_generalize.jpg)

注：最终代码中，泛化关系表现为继承非抽象类；

##### 实现关系(realize)

实现关系用一条带空心箭头的虚线表示；

eg：”车”为一个抽象概念，在现实中并无法直接用来定义对象；只有指明具体的子类(汽车还是自行车)，才 可以用来定义对象（”车”这个类在C++中用抽象类表示，在JAVA中有接口这个概念，更容易理解）

![uml_realize](media/uml_realize.jpg)

注：最终代码中，实现关系表现为继承抽象类；

##### 聚合关系(aggregation)

聚合关系用一条带空心菱形箭头的直线表示，如下图表示A聚合到B上，或者说B由A组成；

![uml_aggregation](media/uml_aggregation.jpg)
聚合关系用于表示实体对象之间的关系，表示整体由部分构成的语义；例如一个部门由多个员工组成；

与组合关系不同的是，整体和部分不是强依赖的，即使整体不存在了，部分仍然存在；例如， 部门撤销了，人员不会消失，他们依然存在；

##### 组合关系(composition)

组合关系用一条带实心菱形箭头直线表示，如下图表示A组成B，或者B由A组成；

![uml_composition](media/uml_composition.jpg)
与聚合关系一样，组合关系同样表示整体由部分构成的语义；比如公司由多个部门组成；

但组合关系是一种强依赖的特殊聚合关系，如果整体不存在了，则部分也不存在了；例如， 公司不存在了，部门也将不存在了；

##### 关联关系(association)

关联关系是用一条直线表示的；它描述不同类的对象之间的结构关系；它是一种静态关系， 通常与运行状态无关，一般由常识等因素决定的；它一般用来定义对象之间静态的、天然的结构； 所以，关联关系是一种“强关联”的关系；

比如，乘车人和车票之间就是一种关联关系；学生和学校就是一种关联关系；

关联关系默认不强调方向，表示对象间相互知道；如果特别强调方向，如下图，表示A知道B，但 B不知道A；

![uml_association](media/uml_association.jpg)
注：在最终代码中，关联对象通常是以成员变量的形式实现的；

##### 依赖关系(dependency)

依赖关系是用一套带箭头的虚线表示的；如下图表示A依赖于B；他描述一个对象在运行期间会用到另一个对象的关系；

![uml_dependency](media/uml_dependency.jpg)
与关联关系不同的是，它是一种临时性的关系，通常在运行期间产生，并且随着运行时的变化； 依赖关系也可能发生变化；

显然，依赖也有方向，双向依赖是一种非常糟糕的结构，我们总是应该保持单向依赖，杜绝双向依赖的产生；

注：在最终代码中，依赖关系体现为类构造方法及类方法的传入参数，箭头的指向为调用关系；依赖关系除了临时知道对方外，还是“使用”对方的方法和属性；

#### 时序图

为了展示对象之间的交互细节，后续对设计模式解析的章节，都会用到时序图；

时序图（Sequence Diagram）是显示对象之间交互的图，这些对象是按时间顺序排列的。时序图中显示的是参与交互的对象及其对象之间消息交互的顺序。

时序图包括的建模元素主要有：对象（Actor）、生命线（Lifeline）、控制焦点（Focus of control）、消息（Message）等等。

关于时序图，以下这篇文章将概念介绍的比较详细；更多实例应用，参见后续章节模式中的时序图；

http://smartlife.blog.51cto.com/1146871/284874


#### 代码加图例
在java以及其他的面向对象设计模式中，类与类之间主要有6种关系，他们分别是：依赖、关联、聚合、组合、继承、实现。他们的耦合度依次增强。

##### 依赖（Dependence） 
![0_13260908301gJS.gif](media/0_13260908301gJS.gif.jpeg)

![0_1326090848k9uU.gif](media/0_1326090848k9uU.gif.png)

依赖关系的定义为：对于两个相对独立的对象，当一个对象负责构造另一个对象的实例，或者依赖另一个对象的服务时，这两个对象之间主要体现为依赖关系。定义比较晦涩难懂，但在java中的表现还是比较直观的：类A当中使用了类B，其中类B是作为类A的方法参数、方法中的局部变量、或者静态方法调用。类上面的图例中：People类依赖于Book类和Food类，Book类和Food类是作为类中方法的参数形式出现在People类中的。

代码样例：

```java
public class People{
    //Book作为read方法的形参
     public void read(Book book){
        System.out.println(“读的书是”+book.getName());
    }
}
```
##### 关联（Association）
![0_13260909884nw0.gif](media/0_13260909884nw0.gif.jpeg)

![0_1326091009mo50.gif](media/0_1326091009mo50.gif.jpeg)

![0_1326091028sK6k.gif](media/0_1326091028sK6k.gif.jpeg)



###### 单向关联

 ![0_13260910603wKT.gif](media/0_13260910603wKT.gif.jpeg)


###### 双向关联

![0_1326091107b7a6.gif](media/0_1326091107b7a6.gif.jpeg)


        对于两个相对独立的对象，当一个对象的实例与另一个对象的一些特定实例存在固定的对应关系时，这两个对象之间为关联关系。关联关系分为单向关联和双向关联。在java中，单向关联表现为：类A当中使用了类B，其中类B是作为类A的成员变量。双向关联表现为：类A当中使用了类B作为成员变量；同时类B中也使用了类A作为成员变量。

代码样例：

```java
public class Son{
   //关联关系中作为成员变量的类一般会在类中赋值
    Father father = new Father();
    public void getGift(){
        System.out.println(“从”+father.getName()+”获得礼物”);
    }
}
 
public class Father{
    Son son = new Son();
    public void giveGift(){
        System.out.println(“送给”+son.getName()+“礼物”);
    }
}
```
##### 聚合（Aggregation）
![0_132609129950Sp.gif](media/0_132609129950Sp.gif.jpeg)

![0_1326091349r4fJ.gif](media/0_1326091349r4fJ.gif.jpeg)




        聚合关系是关联关系的一种，耦合度强于关联，他们的代码表现是相同的，仅仅是在语义上有所区别：关联关系的对象间是相互独立的，而聚合关系的对象之间存在着包容关系，他们之间是“整体-个体”的相互关系。

代码样例：

```java
public class People{
    Car car;
    House house; 
    //聚合关系中作为成员变量的类一般使用set方法赋值
     public void setCar(Car car){
        This.car = car;
    }
    public void setHouse(House house){
        This.house = house;
    }
 
    public void driver(){
        System.out.println(“车的型号：”+car.getType());
    }
    public void sleep(){
        System.out.println(“我在房子里睡觉：”+house.getAddress());
    }
}
```
##### 组合（Composition）

![0_1326091487YvWr.gif](media/0_1326091487YvWr.gif.jpeg)

![0_1326091503n1mJ.gif](media/0_1326091503n1mJ.gif.jpeg)


        相比于聚合，组合是一种耦合度更强的关联关系。存在组合关系的类表示“整体-部分”的关联关系，“整体”负责“部分”的生命周期，他们之间是共生共死的；并且“部分”单独存在时没有任何意义。在下图的例子中，People与Soul、Body之间是组合关系，当人的生命周期开始时，必须同时有灵魂和肉体；当人的生命周期结束时，灵魂肉体随之消亡；无论是灵魂还是肉体，都不能单独存在，他们必须作为人的组成部分存在。

```java
Public class People{
    Soul soul;
    Body body; 
    //组合关系中的成员变量一般会在构造方法中赋值
     Public People(Soul soul, Body body){ 
        This.soul = soul;
        This.body = body;
    }
 
    Public void study(){
        System.out.println(“学习要用灵魂”+soul.getName());
    }
    Public void eat(){
        System.out.println(“吃饭用身体：”+body.getName());
    }
}
```
##### 继承（Generalization）

![0_1326091748FS48.gif](media/0_1326091748FS48.gif.jpeg)

![0_1326091767VEff.gif](media/0_1326091767VEff.gif.jpeg)


        继承表示类与类（或者接口与接口）之间的父子关系。在java中，用关键字extends表示继承关系。UML图例中，继承关系用实线+空心箭头表示，箭头指向父类。

##### 实现（Implementation）

![0_1326091794M0ju.gif](media/0_1326091794M0ju.gif.jpeg)

![0_1326091808887z.gif](media/0_1326091808887z.gif.jpeg)


         表示一个类实现一个或多个接口的方法。接口定义好操作的集合，由实现类去完成接口的具体操作。在java中使用implements表示。UML图例中，实现关系用虚线+空心箭头表示，箭头指向接口。

        在java中继承使用extends关键字，实现使用implements关键字，很直观。就不代码演示了。
