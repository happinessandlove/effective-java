在我们平时的代码中，会发现一个类会依赖其它的类，如一个拼写检查的类会依赖于一个词典类，下面给出几个错误示范：
### 错误示范
##### 错误示范1:
``` java
public class SpellChecker {
    private static final Lexicon dictionary = ...;

    private SpellChecker() {
    } // 该类不可实例化

    public static boolean isValid(String word) { ...}

    public static List<String> suggestions(String typo) { ...}
}
```
##### 错误示范2:
``` java 
public class SpellChecker {
    private final Lexicon dictionary = ...;

    private SpellChecker(...) {
    }// 该类不可实例化

    public static SpellChecker INSTANCE = new SpellChecker(...);

    public boolean isValid(String word) { ...}

    public List<String> suggestions(String typo) { ...}
}
```
##### 错误示范3:
``` java 
public class SpellChecker {
    private Lexicon dictionary = ...;

    private SpellChecker(...) {
    }// 该类不可实例化
    
    public void setDictionary(Lexicon dictionary){
      this.dictionary = dictionary;
    }

    public boolean isValid(String word) { ...}

    public List<String> suggestions(String typo) { ...}
}
```
##### 缺点：
1. 以上两种写法都是假定一个拼写检查的类只会有词典，但事实上对于不同语言是有不同的词典点，很明显以上写法1和2是不支持更换词典的
2. 测试时需要一个特殊的词典进行测试，写法1和2也是不支持的
3. 为了解决上述的问题，我将dictionary字段改为非final,并提供set方法对其进行赋值，这种写法在多线程环境中会是代码发生不可预见的错误。
#### 正确示范:
``` java
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) { ...}

    public List<String> suggestions(String typo) { ...}
}
```
这种写法就是通过构造方法进行依赖注入，它有如下优点：
##### 优点：
1. 可以在新建对象时选择需要依赖的对象，并且dictionary属性还是final,这在保证灵活的同事还提供了安全性。

#### 实践建议：
1. 构造方法的参数最好能够使用工厂类
2. 在java8中可以使用Supplier<T>这个接口通过lambada表达式提供需要的注入对象
