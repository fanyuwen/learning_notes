## 静态方法

1. 接口定义的静态方法 **jdk1.8**
   > 从java8开始,可以在接口中定义静态方法,但是无法通过实现的子类(包括子接口)去访问方法,因为java接口是支持多实现(对于子接口来说是多继承)的,一旦多个父接口定义方法签名一样的方法,就不知道该调用谁了
   ```java
      public interface StaticMethod{
         static void fun(){
            System.out.println("I'm interface's static method.");
         }
      }
      public interface SonStaticMethod extends StaticMethod{
      }
      public class TestStaticMethod implements StaticMethod {
          public static void main(String[] args){
              //该调用编译会报错
              TestStaticMethod.fun();
              //子接口也无法调用,该调用也会编译报错
              SonStaticMethod.fun();
              //根据对象去调用也会报错,原因同上,本身java不主张通过对象去调用静态方法
              StaticMethod staticMethod = new TestStaticMethod();
              staticMethod.fun();
          }
      }
   ```
2. 类定义的静态方法
   > 类定义的静态方法,可通过类直接去调用,调用哪个类就是哪个类的静态方法,也可以通过对象去调用,此时也是根据对象的类型去判断调用哪个静态方法
   ```java
      public abstract class ClassStaticMethod{
          static void fun(){
              System.out.println("I'm abstract class's static method.");
          }
      }
      class AnotherSonClassStaticMethod extends ClassStaticMethod{
      }
      class SonClassStaticMethod extends ClassStaticMethod{
          //该行为不是重写,而是隐藏,针对类的字段和内部类是相同的道理
          static void fun(){
              System.out.println("I'm son class's static method.");
          }
          public static void main(String[] args){
              //调用父类的静态方法
              ClassStaticMethod.fun();
              //调用父类的静态方法,子类在没有定义同方法签名的静态方法时是可以从父类中继承该静态方法的,所以这里可以通过子类的类名访问父类的静态方法
              AnotherSonClassStaticMethod.fun();
              //对象调用同理,虽然不推荐
              new AnotherSonClassStaticMethod().fun();
              //调用子类的静态方法
              SonClassStaticMethod.fun();
              //同上,不推荐
              new SonClassStaticMethod().fun();
              //调用的是父类的静态方法,静态属性的访问是根据访问实体的类型(声明的类型),而不是实际引用的类型,下面的访问实体的类型是父类,所以是访问父类的静态方法,一样是不推荐
              ClassStaticMethod classStaticMethod = new SonClassStaticMethod();
              classStaticMethod.fun();
          }
      }
   ```

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
**以上两种也是出现了同一个类中,会出现方法名和方法参数一样,但是返回值不一样的方法,只是这种方法是编译器生成的,开发是无法编写**
+ lambda表达式
```java
    //以下是按照jdk1.8的实际测试
    class Lamdba{
        void fun(String args){
            Predicate<String> predicate = s -> s.endsWith(args);
        }
        //会生成下面的一个合成方法,感觉如果一个类里面使用的lambda表达式很多的话,这个类还是挺容易膨胀的
        // private static boolean com.xxx.xxx.Lamdba.lambda$fun$1(java.lang.String,java.lang.String){...}
  
        //如果lambda表达式赋值为方法引用则不会出现合成的方法
        //其它情况下都会出现合成方法,静态属性、成员属性、成员方法里、静态方法里定义的
        //生成的合成方法名为private static lambda的返回值 所在类的全限定名.lambda$ + 所在方法名(成员属性和构造函数为new,静态属性为static) + $递增的序号值
    }
```

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
           methodOverload.moorge(dessertRef, sconeRef);// 1
           methodOverload.moorge(chocolateCakeRef, dessertRef);// 2
           methodOverload.moorge(chocolateCakeRef, butteredSconeRef);// 3
           methodOverload.moorge(cakeRef, sconeRef);// 4 INVALID
        }
    }
```
+ 第一、二个调用:  
  第一个调用使用第一种形式的moorge,因为参数和自变量类型刚好匹配  
  第二个调用使用第二种形式,因为没有一种刚好匹配,只有在第二种形式中,所提供的自变量可以赋值给参数类型  
  **以上两种情况,在上述方法匹配算法第一步结束之后,要调用的方法就很清楚了**  
+ 第三个调用:  
  第三个调用潜在的重载方法包括所有三种形式,因为`chocolateCakeRef`可以赋值给第一个参数的任一种类型,`butteredSconeRef`也可以赋值给第二个参数的任一种类型,
  而没有一种刚好匹配,所以第一步之后,仍然有三种待选的方法要求从待选的方法中去掉比较不具体的方法,在这种情况下,第一种形式被去掉,因为第三种形式更具体,
  `chocolateCakeRef`可以赋值给第一种形式的`Dessert`参数,`Scone`可以赋值给第一种形式的`Scone`参数,所以第一种比较不具体,同样第二种形式也从待选的方法中去掉,
  这样,可能的方法已经减为只有一种,即moorge的第三种形式,从而将使用这种调用方式
+ 第四个调用
  最后一个调用是非法的,第一步之后,待选的方法还有第一和第二种形式,因为没有哪一种形式的参数可以赋值给另一种参数,所以第二步之后哪一种也不能从待选的方法中去掉,因而这是一种
  编译器无法决定的模糊调用,所以调用非法(一种非法moorge调用),可以通过显式*强制转换*一个方法实参为`Dessert`类型来解决这种模糊调用(*如果**强制转换**成`Cake`类型,就调用第一种形式,如果**强制转换**成`Scone`类型,就调用第二种形式*)

以上规则也适用于基本类型,例如: `int`变量可以赋值给`float`变量,就像我们考虑`butteredSconeRef`可以赋值给`Scone`引用一样,对它们也可用同样思路来决定调用哪个重载方法,但是,
*隐式地把整型转换成更小的类型就不行--如果某个方法要求`short`类型参数,但是想给它提供一个`int`实参,就必须显式地把它强制转换为`short`类型,它不匹配`short`参数,不管其值如何(数值是否在类型能表示的范围内)*

编译时,根据对象引用声明的类型和实际参数值来运行这个方法解析的过程,这个过程确定了方法调用的形式,但并不能确定方法的实现(重载和重写的区别),运行时,上面提到的方法所调用的对象的实际类型,将查找在编译时确定的方法实现
*方法并不仅仅在返回类型或所抛出的异常上有区别,因为在决定所需的重载方法时有太多的不明确性,例如: 如果两个方法的区别仅在于一个返回`int`而另一个返回`short`,那么在下列语句中它们有相同的意义:`double d = method();`*
*当调用重载的方法时,调用处的代码可能会捕获该方法抛出的任何异常,如果实际没有抛出异常的话就一个也无法捕获,所以,这里也存在一个关于异常的问题,就抛出异常的差别而言,无法确定使用两个方法中的哪一个*
**这些模糊二义性并不是总能在编译时就察觉到的,例如:超类中可能修改以加入新的方法,而这个方法仅和继承类中的某个方法的返回类型不同,如果这个继承类没有再编译,这个问题可能就不会觉察到,运行时,因为方法调用的具体确切形式是在编译时就确定的,
所以就会在继承类中搜索这个方法,这就不会有任何问题,同样,如果类中要加入方法的重载形式,但是调用这个方法的类未经编译,则这个新方法就不会被那个类调用,将调用的方法的形式在编译时就已经确定,并且这种形式不同于那个新方法的形式**

方法重载与基本类型拆装箱的问题
```java
    public class Box{
        void invoke(){
            //在实际调用过程中,需要注意
          numBox(1);//调用的是下面的
          Integer i = 1;
          numBox(i);//调用的是上面的
        }
        //下面两个方法属于是重载的方法,只是类型是包装和基本类型
        static void numBox(Integer i){}
        static void numBox(int i){}
    }
```