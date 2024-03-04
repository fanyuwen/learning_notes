## 类常量初始化的一个问题
偶然写代码发现一个问题,在常量初始化的时候,如果是按照类名.属性名去初始化会报错,**Cannot assign a value to final variable 'XXX'**,只能直接按照名称去
访问
```java
    public class Class{
        //定义一个常量属性,但是没初始化,一般这种不是直接定义+初始化的常量是需要在静态代码初始化块里明确初始化的
        //尤其是对于各种分支场景(if/else、switch/case、try/catch等)
        private static final int property;
        
        static {
            //Class.property = 1;//这里会报错!不知道原因(jdk1.8),目前来看直接通过属性名的方式和通过类型.属性名的方式还是有区别的
            property = 2;//只能直接通过名称去访问
        }
    }
```

## 类静态初始化访问顺序问题
当静态初始化块在静态属性(或者常量)定义之前定义,在代码块里去访问这个静态变量(或者常量),会根据使用的方式不同有不同的情况
```java
    public class Class{
        //先定义静态初始化块
        static {
            /*
             * 对常量进行访问,对常量的初始化也是上面的情况(类名.属性名初始化会报错,直接属性名初始化可以)
             */
            //Class.property = 1; -> 报错,错误信息跟上面的一样
            //property = 1; -> 没有问题,前提是常量没有初始化
            System.out.println(Class.property);//这里访问没问题
            System.out.println(property);//这里会报错 Illegal forward reference(非法前向引用)
            
            /*
             * 对静态属性进行访问
             */
            System.out.println(Class.property2);//这里访问没问题
            System.out.println(property2);//这里会报错 Illegal forward reference(非法前向引用)
            Class.property2 = 2;//但是这个对它进行写入(赋值)是没有问题的
            property2 = 3;//这种方式也是没有问题,可以进行编译
        }
        //静态属性定义在下面,先将常量进行初始化好
        private static final int property = 1;
        private static int property2;
    }
```

## 类初始化死锁问题
一般类继承体系中,当类加载去加载子类的时候,也是会先加载父类的信息,如果父类中有引用具体的子类,则有可能出现类加载死锁问题
```java
    public class Father {
        static {
            /*
             * 无论是直接在静态代码块里创建子类对象还是引用子类的静态属性,都会存在类加载死锁问题
             * Referencing subclass Son from superclass Father initializer might lead to class loading deadlock
             */
            System.out.println(new Son());
            System.out.println(Son.name);
        }
    }
    public class Son extends Father{
        static String name;
    }
```
死锁产生的场景:A线程去加载子类型,B线程去加载父类型,此时因为继承关系A线程也要去加载父类型,B线程的父类型因为有子类型的初始化逻辑所以也必须要加
子类型所以就产生了死锁
而如果是在动态代码块里就不会出现死锁的情况,因为父类加载子类的时候是在创建父类对象的时候,这个时候子类已经加载完毕,并且子类加载的时候因为继承关系
去加载的父类信息也是另一个线程加载完毕的