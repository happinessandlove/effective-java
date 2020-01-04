在使用不可实例化的类时，强制使用私有构造方法，这样这个工具类就不可以进行实例化。
``` java
public class UtilityClass {
        private UtilityClass() {
            throw new AssertionError();
        }
    }
```
##### 好处：
1. 保证该类在任何情况下都不能被实例化
2. 不误导使用者认为该类的使用需要实例化
##### 缺点：
1. 该类不可以被继承
