> 定义两个接口 A B,A B分别定义一个方法(方法名和参数列表相同,返回值不同并且没有继承关系)如下,  
> 定义一个类去同时实现这两个接口,会报错  
> <p style="color:red">(clashes with foo,attempting to use incompatible return type)</p> 两个实现方法冲突  
> 目前想到的解决办法是实现方法返回的类型是它们的共同子类型

```java
interface A {
    Serializable foo();
}

interface B {
    CharSequence foo();
}

class Test implements A, B {
    @java.lang.Override
    public Serializable foo() {//这里会报错
        return null;
    }

    @java.lang.Override
    public CharSequence foo() {//这里也会报错
        return null;
    }
}

class Test1 implements A, B {
    @java.lang.Override
    public String foo() { //这里不会报错
        return null;
    }
}
```
> 上面的问题衍生出了另一个问题,关于范型的,如下面代码
```java
interface C<T>{
    T foo();
}

interface D extends C<Serializable>{}
interface E extends C<CharSequence>{}

class Test implements D,E{ //报错,因为不能同时继承(实现)同一个范型的不同类型实参
    @java.lang.Override
    public String foo() {
        return null;
    }
}
```
> 表面上是跟上面一样的问题,但其实因为范型的本身约定,导致上面编译报错
