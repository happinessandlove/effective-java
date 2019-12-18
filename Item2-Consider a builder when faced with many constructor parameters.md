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
* 调用时代码易读。
#### 2.2 缺点<br>
* 调用时比较啰嗦。
* 必须要设置的值不能进行验证，这样会使新对象处于一个不稳定的状态，如果忘记设置必须要设置的值，则会导致程序出错。
* 不可以创建不可变对象，即成员变量都是final修饰，这种javabean模式的构造函数在多线程环境会出现问题。

*那么问题来了，有没有什么方式能够拥有以上两种方式点不足，并且又具有它们的优点呢，是有的，它就是我们的builder。*<br>

### 3.建造模式创建对象
**example1:**<br>
*不需继承的场景*<br>
``` java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 必填参数 
        private final int servingSize;
        private final int servings; 
        // 可选参数，设置默认值 
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```
调用：<br>
``` java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100)
                .sodium(35)
                .carbohydrate(27)
                .build();
```                
*需要继承继承的场景*<br>
抽象类
``` java
public abstract class Pizza {
    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}

    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build(); 

        protected abstract T self(); // 子类必须实现此方法并返回this
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
        }
}
```
子类1：
``` java
public class NyPizza extends Pizza {
    public enum Size {SMALL, MEDIUM, LARGE}

    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
```
子类2：
``` java
public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // 默认值

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override
        public Calzone build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }
}
```
调用：
``` java
NyPizza pizza = new NyPizza.Builder(SMALL)
     .addTopping(SAUSAGE)
     .addTopping(ONION)
     .build();
Calzone calzone = new Calzone.Builder()
      .addTopping(HAM)
      .sauceInside()
      .build();
```

#### 3.1 优点<br>
* 可以灵活的选择需要的构造参数，拥有javabean方式的优点，弥补伸缩式构造方式的不足
* 必填的参数在构造时必须要传入，弥补javabena方式的不足，拥有伸缩式构造方式的优点
* 可读性比较好。
#### 3.2 缺点<br>
* 会消耗一些性能。
