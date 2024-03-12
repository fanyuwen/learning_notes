# 这里总结了一些java语言很小众、很冷门的知识点或者用法或者窍门

+ 不写main方法来执行程序,通过静态初始化代码块
```java
    public class Main{
        static {
            System.out.println("Hello!");
        }
        
        //通过静态初始化属性调用方法里去实现逻辑也能实现
        private static int property = getInt();
        
        static int getInt(){
            System.out.println("World!");
            return 1;
        }
    }
```
+ (**jdk1.8**) 动态内部类里无法定义静态内容,但是可以定义编译期常量(在编译器就能确定的值)
```java
    public class Outer{
        class Inner{
            //private static int propertyInt = 2;编译报错
            private static final int propertyInt2 = 2;//编译器常量是可以的
            private static final int propertyInt3 = getInt();//运行时常量不支持
            
            // static class InnerInner{} 同样静态内部类也不支持
        }
    }
```
+ 可以设置`System.out`属性改变实际打印的逻辑,同样的逻辑也适用于`System.in`和`System.err`
```java
    class System{
        //...省略
        //可以发现它们都是final的但是依然可以通过调用对应的set方法(静态地)进行修改,所以这里是为数不多的可以修改final修饰的属性
        //通过native(本地)方法去实现的,这个是可以绕过java语言编译器的限制的
        public final static PrintStream out = null;
        public final static PrintStream err = null;
        public final static InputStream in = null;
        //...省略
        //set方法的参数为对应的InputStream、PrintStream实现
        public static void setIn(InputStream in);
        public static void setOut(PrintStream out);
        public static void setErr(PrintStream err);
    }
```
+ java语言是支持unicode字符集,对于代码的解析,它也是根据编码去解析的,如果我们采用unicode的数字编码的格式去写代码,会造成一些肉眼无法直观看到的东西
```java
    class Unicode{
        //《java核心技术》卷一 里也有提到这个概念
        //下面的代码可以正常运行并打印8
        public static void unicodeShow() {
            int i = 5;
            // #start unsafe# http://java.sun.com\u000a\u002f\u002a
            int *ip = &i;
            *ip += 3;
            i = *ip;
            // #end unsafe# http://java.sun.com\u002a\u002fi+=\u0033\u003b
            System.out.println(i);
        }

        //下面这个方法是上面的unicode字符转成对应的字符的结果
        /*\u000a -> 代表回车字符 (这个字符很特殊,因为是回车,所以无法赋值给一个变量)
                    char c = '\u000a'; 这个是错误的 */ 
        //\u002f -> /
        //\u002a -> *
        //\u0033 -> 3
        //\u003b -> ;
        public static void unicodeShow() {
            int i = 5;
            // #start unsafe# http://java.sun.com
            /*
                int *ip = &i;
                *ip += 3;
                i = *ip;
                // #end unsafe# http://java.sun.com*/i+=3;
            System.out.println(i);
        }
    }
```
+ 八进制数字字面量,与直观看到的数字会存在差异
```java
    public static void main(String[] args) {
        System.out.println(012);//输出结果为10,因为任何以0开头的数字字面量都被解释为8进制数据
    }
```
+ `double`和`float`类型
   `NaN`值,double和float类型的一个特殊值(Not a Number,不是一个数)
   产生方式 `0.0 / 0.0`(double)或者`0.0f / 0.0f`(float),`NaN`与其它值运算产生的结果也是`NaN`,
   `NaN == NaN`的结果是`false`(有一个题目: 请通过声明初始化变量i让下面的代码变成死循环`while(i != i){}`)
+ Array(数组)
  有一个通过*可变参数*获取泛型实际类型的方式:
  ```java
    public final class Capture<E>{
        private Class<E> type;
        @SafeVarargs
        @SuppressWarnings("unchecked")
        public Capture(E... args){
            if(args == null){//保护性判断,可能为null
                this.type = (Class<E>)Object.class;
                return;
            }
            this.type = (Class<E>)args.getClass().getComponentType();
        }
        public Class<E> getType(){
            return type;
        }
    }
  ```
+ xor(异或运算符)(可以把该运算符运用到逻辑判断中)
  ``if(a == null ^ b == null){throw new NullPointException();}``只有a或者b有一个为null才会报空指针,都为null或者都不为null不会报空指针
+ 整数溢出
  整数类型(byte、short、int、long)都有各自的数值范围,如果运算超出范围会溢出
  `(byte|short|int|long).min_value == (byte|short|int|long).max_value + 1`
  `Math.abs()`绝对值问题,`Math.abs(min_value)`结果还是`min_value`,`Math.abs`并不是一定会给你返回正数,原因还是数值的范围,最小值 = -(最大值+1),所以对最小值取-的结果就是最大值+1,还是最小值
+ try-with-resources(java自动资源管理机制),被try管理的资源不管代码块如何退出,都能被关闭
  任何实现了`java.lang.AutoCloseable`实现close方法(`java.io.Closeable`也继承了这个接口),这个也是可以适用适配器模式场地的地方,因为很多老的应用资源并没有实现这个接口
  在try后面声明的多个资源,close方法的调用顺序与它们声明的顺序相反
  ```java
     void tryWithResources(
         Resource r1 = Resource();
         Resource r2 = Resource();
         Resource r3 = Resource();
     ){
         r3.close();
         r2.close();
         r1.close();
     }
  ```
