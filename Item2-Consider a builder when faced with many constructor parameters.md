# 当一个类构造参数较多时考虑使用构造器来构造对象
前言：首先介绍一下两种我们经常使用的用于构造一个对象的方法，并说明一下它们各自的优缺点，最后介绍构造器的使用，并说明它的优缺点。<br>
### 1. 使用伸缩式构造函数来进行函数的构造
**example:**<br>
``` java
public class NutritionFacts {
    private final int servingSize; // (mL) 必须初始化 
    private final int servings; // (per container) 必须初始化 
    private final int calories; // (per serving) 可选 
    private final int fat; // (g/ serving) 可选 
    private final int sodium; // (mg/ serving) 可选 
    private final int carbohydrate; // (g/ serving) 可选 

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}
```
调用：<br>
``` java
NutritionFacts cocaCola = new NutritionFacts( 240, 8, 100, 0, 35, 27);
```
#### 1.1 优点<br>
* 编构造方法简单
#### 1.2 缺点<br>
* 调用者很难使用，需要一一核对传入的值对应哪一个参数，并且往往搞不清该调用哪个构造方法。
* 代码可读性不高。
* 会导致隐藏bug，比如在上面的例子中构造参数都是int类型，比如不小心把两个参数的位置搞反，那么编译器不会报错，但是程序运行会出现问题。

### 2. 使用JavaBean来进行函数的构造
**example:**<br>
``` java
public class NutritionFacts {
    // 如果参数有默认值，则进行初始化
    private int servingSize = -1; // 没有默认值，构造对象时必须进行赋值 
    private int servings = -1; // 没有默认值，构造对象时必须必须赋值
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;

    public NutritionFacts() {
    }

    // Setters
    public void setServingSize(int val) {
        servingSize = val;
    }

    public void setServings(int val) {
        servings = val;
    }

    public void setCalories(int val) {
        calories = val;
    }

    public void setFat(int val) {
        fat = val;
    }

    public void setSodium(int val) {
        sodium = val;
    }

    public void setCarbohydrate(int val) {
        carbohydrate = val;
    }
}
```
调用:<br>
``` java
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```
#### 2.1 优点<br>
* 调用时代码易读
#### 2.2 缺点<br>
* 调用时比较啰嗦
* 
