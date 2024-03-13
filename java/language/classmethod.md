## 方法重写与返回值类型不同的问题
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
> 表面上是跟上面一样的问题,但其实因为范型的本身约定,导致上面编译报错,并且无法通过重写去解决

## 桥接属性(合成属性)
java编译器会在编译阶段出于一些辅助功能的实现而会在类的实现上添加一些辅助的属性(方法或者字段)
+ 成员内部类
```java
    class Outer{
        class DynamicInner{
            //private final Outer outer; 这个属性是java编译器自动生成的,为的就是在创建内部对象的时候必须要获得对外部对象的引用
        }
    }
```
+ 方法重写
```java
    class Father {}
    class Son extends Father{}
    class MethodOverride {
        Father getInfo(){
            
        }
    }
    class MethodOverride {
        //jdk1.5开始支持方法重写返回值的协变(支持返回类型是父类返回类型的子类型)
        @Override
        Son getInfo(){
            
        }
        //java编译器也会生成一个方法来转发(桥接)到子类实际实现的方法
        Father getInfo(){//java编译器生成的方法
            return getInfo();//调用实际的方法
        }
    }
```
+ 泛型实现(泛型实际参数)
```java
    //无论是类还是抽象类亦或是接口
    class Generic<T>{
        T produceGeneric(){}
        void consumerGeneric(T param){}
    }
    class Actual{}
    class ActualImpl extends Generic<Actual>{
        Actual produceGeneric(){}
        Object produceGeneric(){//java编译器辅助生成,为了兼容父类的类型(泛型擦除)
            return produceGeneric();
        }
        void consumerGeneric(Actual param){}
        void consumerGeneric(Object param){//java编译器辅助生成,为了兼容父类的类型(泛型擦除)
            consumerGeneric((Actual)param);
        }
    }
```
+ lambda表达式
