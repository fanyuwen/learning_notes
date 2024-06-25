### java方法重载和可变参数
**可变参数只能是方法的最后一个参数**
```java
    //如果一个类当中同名方法的参数一个是数组类型,一个是可变参数类型,则编译会报错(说该方法已经定义)
    //因为可变参数实际上就是会转变为数组形式
    void fun(Object[] args){}
    
    void fun(Object... args){}
```

### java方法重写和可变参数
```java
    //可变参数在方法重写的时候是可以和数组变换使用
    class Father {
        void fun(Object[] args){}
        void fun1(Object... args){}
    }
    class Son extends Father {
        void fun(Object... args){}
        void fun1(Object[] args){}
    }
```

### 可变参数的一些问题
+ 可变参数如果不传参数,会创建一个空数组,但是如果直接传null,则方法实际接受的也是null
```java
    public class Varargs{
        public static void main(String[] args) {
            args();//输出0,因为没有传参数,会创建一个空数组对象
            args(null);//会报空指针异常,因为实际接受的参数也是null
        }
        
        public static void args(Object... args){
            System.out.println(args.length);
        }
    }
```
+ 可变参数在方法重载的时候的方法
```java
    public class Varargs{
        public static void main(String[] args) {
            //如果能够精确匹配则首先匹配最精确匹配的方法,也就是第一个没有可变参数的方法
            args(new Object(), new Object());
            //这边编译会报错,因为这种调用能够匹配下面2个可变参数方法,产生了2义性,编译器不知道该调用哪个
            args(new Object(), new Object(), new Object());
        }
    
        public static void args(Object a1, Object a2){
            System.out.println(args.length);
        }
        public static void args(Object a1, Object... args){
            System.out.println(args.length);
        }
        public static void args(Object a1, Object a2, Object... args){
            System.out.println(args.length);
        }
        
    }
```
