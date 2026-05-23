## 对象初始化顺序
创建对象的初始化顺序,包含继承关系的初始化,子类对象创建的时候会先调用父类的构造方法(因为每个子类在没有显示调用自己其它构造方法时,会隐式通过`super()`调用父类的构造方法),
如果父类对象也是继承其它对象的话就会继续往上调用,直到父类为`Object`,次数当前对象会根据申明顺序依次调用本身的属性初始化、初始化块,然后执行当前的构造函数的逻辑,执行完整个
构造函数之后调用链回到子类的构造函数中,再依次根据声明顺序调用当前子类的属性初始化、初始化块和构造方法的逻辑,直到返回到最终创建的子类对象中
```java
/**
 * 父类
 */
class Father{
    //父类的属性
    private int fatherProp = initProp();
    
    private static int initProp(){
        System.out.println("init father's prop.");
        return 1;
    }

    {
        System.out.println("invoke father's init block.");
    }
    
    //父类的构造方法
    Father(){
        System.out.println("invoke father's constructor.");
    }
}

/**
 * 子类
 */
class Son extends Father {
    //这边特别要注意声明顺序的问题,如果初始化块的声明顺序在属性初始化之前,则会先调用初始化块
//    {
//        System.out.println("invoke son's init block.");
//    }
    //子类属性
    private int sonProp = initProp();

    private static int initProp(){
        System.out.println("init son's prop.");
        return 1;
    }

    {
        System.out.println("invoke son's init block.");
    }
    
    //子类的构造方法
    Son(){
        //super();这边是隐式调用的,只要不是调用本类中的其它构造函数
        System.out.println("invoke son's constructor.");
    }
}

class Main{
    static void main(String[] args) {
        //执行Son的构造函数会依次打印:
        //1. init father's prop.
        //2. invoke father's init block.
        //3. invoke father's constructor.
        //4. init son's prop.
        //5. invoke son's init block.
        //5. invoke son's constructor.
        new Son();
    }
}

```