### 5个优势:
#### 1. 静态工厂方法有自己的名字，使其对调用者更加友善。<br>
**example:**<br>
``` java
public class MyClass {
    private String desc;

    public MyClass(String desc) {
        this.desc = desc;
    }

    public static MyClass getInstance(String desc) {
        MyClass myClass = new MyClass(desc);
        return myClass;
    }
}
```
使用如下:<br>
通过构造函数获取类实例：`MyClass myClass = new MyClass("demo");`
通过静态工厂方法获取类实例：`MyClass myClass = MyClass.getInstance("demo");`
可以看出静态工厂方法语义上更加容易让人理解。
#### 2. 使用类的构造方法获取类实例每次都是获得一个新的对象，而使用静态工厂方法可以实现单例，即每次获取的是相同的类实例。<br>
**example:**<br>
`Boolean.valueOf(boolean)`的实现就是一个单例的静态工厂方法，这种场景下可以避免重复创建对象，节省内存。<br>
#### 3. 使用静态工厂方法可以返回定义的返回类型的任意子类。<br>
**example：**<br>
`java.util.Collections`类中实现了42个集合类，这些集合类不是公有的，即不可以通过类的初始化方法获得，他们都是通过静态工厂方法进行返回，这些静态工厂方法的返回类型定义的都是不同集合类型的接口，具体实现时则可以返回这些接口的子类，大大增加了灵活性。<br>
#### 4. 使用静态构造方法获取类的实例可以在不改变静态工厂方法签名的情况下，依据参数的不同来获取不同的子类，这样带来的好处是随着版本的更迭，调用者不需要去关心内部改变了什么，只需要像原来那样调用就行。<br>
**example：**<br>
`jdk`中的`EnumSet`抽象类，它没有提供任何公有的实现类，而是通过其内部的`noneOf`静态方法返回一个空的`EnumSet`的子类，该方法会根据传入的`enum`的长度来决定返回的是`RegularEnumSet`还是`JumboEnumSet`（`EnumSet`一共就这两个实现类），如果在后续jdk版本中，增加了新的`EnumSet`实现类，那么调用者根本不用关心获取的是哪个实现类，还是像原来那样使用`EnumSet.noneof(Season.class)`来进行调用即可。<br>
#### 5. 静态工厂方法返回类型不需要是已经存在的。<br>
这主要是指可以使用java反射技术去返回具体实例<br>
**example：**
``` java
interface MyInterface {
        void info();
    }

public static MyInterface getInstance() throws ClassNotFoundException, IllegalAccessException, InstantiationException {
    return (MyInterface)Class.forName("chapter2.MyInterfaceImpl1").newInstance();
}
```
`MyInterfaceImpl1`是`MyInterface`的具体实现类，在我写这个`getInstance`静态工厂方法时这个实现类可以不存在。<br>
### 2个劣势:
#### 1. 使用静态工厂方法的类一般没有`public`或者`protected`的构造方法，这意味着该类不能够被继承，但俗话说，塞翁失马焉知非福，在这种约束下，这要求调用者必须使用组合而非继承来使用该类。<br>
#### 2. 静态工厂方法不太容易被找到，因为类肯定可以通过构造方法来获取实例，但有没有静态工厂方法却是不明确的。java官方文档也没有将静态工厂方法放在显眼的位置，但是构造方法确实放在开头很显眼的位置。<br>
### 静态工厂方法的建议命名:
1. from<br>
2. of<br>
3. valueOf<br>
4. getInstance<br>
5. newInstance<br>
6. get{}/new{} //{}可以是任意你要得到的类型<br>
