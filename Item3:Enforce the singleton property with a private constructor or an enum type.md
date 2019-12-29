### 单例实现方式1：final类变量方法
``` java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() {
        // to do
    }

    public void leaveTheBuilding() {
        // to do
    }
}
```
##### 实践建议：
在`AccessibleObject.setAccessible`方法的帮助下，可以利用反射机制调用私有构造方法，因此为了避免这种攻击，我们需要在私有构造方法中判断是否已有实例，如有则抛出异常。<br>
``` java
private Elvis() {
        if (INSTANCE != null) {
            new Exception("dumplcate object").printStackTrace(); 
        }        
}
```

### 单例实现方式2:静态工厂方法
``` java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() {
        // ...
    }

    public static Elvis getInstance() {
        return INSTANCE;
    }

    public void leaveTheBuilding() {
       // ... 
    }
}
```
##### 优点：
1. 可以从API`getInstance`很明确的知道它是单例
2. 它很简单
3. 灵活性很高，可以改造内部实现而不改变对外实现，比如可以实现懒加载
4. 可以再此基础上实现通用单例工厂（敬请期待Item 10）
5. 可以使用java8的新特性Supplier
``` java
Supplier<Elvis> supplier = Elvis::getInstance;
Elvis elvis = supplier.get();
```
##### 实践建议：
实现单例模式的序列化的时候，需要注意序列化会多次实例化类，因此要实现彻底的单例需要使用关键字`transient`修饰类变量和实力变量，并实现如下方法<br>
``` java
private Object readResolve() {
        // 返回一个真实的Elvis让垃圾收集器关注该实例 
        return INSTANCE;
    }
```

### 单例实现方式3：枚举类方法
``` java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() { 
        // ...
    }
}
```
##### 优点：
1. 简洁
2. 不需要自己实现序列化和反序列化方法
3. 自身机制提供单例保证
4. 可以面对负责的序列化和反射攻击场景
##### 实践建议：
这种实现方式看起来有点奇怪，但这个是最好的实现方式。但当你需要继承类和接口是，这种方法不适用。
