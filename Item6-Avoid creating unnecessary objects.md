我们在写程序中经常会创建很多类，但事实上很多类是重复的，并且是可以重复利用的，下面给出集中常见的类滥用的例子。
##### 错误示范1:
``` java
String s = new String("bikini");
```
##### 正确示范1:
``` java
String s = "bikini";
```
##### 错误原因:
######## 错误示范中如果在循环或方法中被重复调用，会重复创建对象，而正确示范则不会，只会有一个"bikini"。
---
##### 错误示范2:
``` java
static boolean isRomanNumeral(String s) {
        return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    }
```
##### 正确示范2:
``` java
public class RomanNumerals {
        private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

        static boolean isRomanNumeral(String s) {
            return ROMAN.matcher(s).matches();
        }
    }
 ```
 ##### 错误原因:<br>
 错误示范中每次调用matches函数都会创建一个Pattern对象，并且只会用一次。
---
 ##### 错误示范3:
 ``` java
 private static long sum() {
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) {
             sum += i;
        }
        return sum;
    }
 ```
 ##### 正确示范3:
 ``` java
 private static long sum() {
        long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) sum += i;
        return sum;
    }
 ```
##### 错误原因:<br>
错误示范中每次进行累加时都会进行拆箱和装箱操作，会耗费性能。
