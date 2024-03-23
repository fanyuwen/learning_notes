### java方法重载和可变参数
```java
    //如果一个类当中同名方法的参数一个是数组类型,一个是可变参数类型,则编译会报错(说该方法已经定义)
    //因为可变参数实际上就是会转变为数组形式
    void fun(Object[] args){}
    
    void fun(Object... args){}
```

### java方法重写和可变参数
```java
    //可变参数在方法重写的时候是可以数组和变换使用
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