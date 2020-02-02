虽然Java提供了内存垃圾回收器，但是在我们写程序时还是会出现内存泄漏的问题，下面举几个例子说明
##### 错误示范1
``` java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) throw new EmptyStackException();
        return elements[--size];
    }

    /**
     * Ensure space for at least one more element, roughly * doubling the capacity each time the array needs to grow.
     */
    private void ensureCapacity() {
        if (elements.length == size) elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```
###### 原因
在进行出栈操作（pop)时，没有将出栈的元素删除，这会导致内存泄漏，因为这对垃圾收集器是透明的，是这个类自我管理的内存空间，修改方案如下：
``` java
public Object pop() {
    if (size == 0) throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference return result;
}
```
##### 错误示范2
缓存也是java内存泄漏的高发点，经常会忘记缓存已经不使用了。<br>
解决方案：<br>
场景1:当引用不被使用时，引用对象将被删除，使用WeakHashMap
场景2：当引用对象不被使用时，引用对象将被删除，使用后台进程、LinkedHashMap的removeEldestEntry方法或java.lang.ref
##### 错误示范3
监听和回调也是java内存泄漏的高发点，它们经常会不会显示的取消注册，解决方案是使用弱引用，如WeakHashMap
