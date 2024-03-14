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

## 方法重载 (摘自《java编程语言(第三版)》6.9.1)
方法重载是指针对方法名相同,但是参数列表的数量、类型或者顺序不同的方法之间称为方法重载,方法的重载调用哪个方法是在编译期确定的
一般来说参数类型不同、参数数量不同编译器是可以很容易区分具体的调用,但是针对相同数量的参数,情况就比较复杂了,java采用"most specific"(最具体)
算法进行匹配:
1. 找出可能适用这种调用的所有方法,也就是具有正确名字的所有重载方法,它们的参数属于可以有实际参数赋值的类型,如果有一种方法的参数和实际参数完全匹配,
   就调用这种方法
2. 如果一个候选方法的参数可以赋值给另一个候选方法的参数,另一个候选方法就从待候选方法中删去,因为它比较不具体,然后重复此过程直到再删不掉别的方法
3. 如果最终只留下一个方法,它就是最具体的,也就是要调用的方法,如果留下一个以上的方法,表示调用模糊不清,没有具体的方法可以调用,调用代码无效,编译报错
```java
    class Dessert{}
    class Cake extends Dessert{}
    class Scone extends Dessert{}
    class ChocolateCake extends Cake{}
    class ButteredScone extends Scone{}
    
    final class MethodOverload {
        //下面是几个重载方法 
        void moorge(Dessert d, Scone s);
        void moorge(Cake c, Dessert d);
        void moorge(ChocolateCake cc, Scone s);
    }
    final class MethodOverloadInvoke {
        private MethodOverload methodOverload = new MethodOverload();
        void invoke(){
           Dessert dessertRef = new Dessert();Cake cakeRef = new Cake();
           Scone sconeRef = new Scone();ChocolateCake chocolateCakeRef = new ChocolateCake();
           ButteredScone butteredSconeRef = new ButteredScone();
           //下面是实际方法调用
           methodOverload.moorge(dessertRef, sconeRef);
           methodOverload.moorge(chocolateCakeRef, dessertRef);
           methodOverload.moorge(chocolateCakeRef, butteredSconeRef);
           methodOverload.moorge(cakeRef, sconeRef);
        }
    }
```
