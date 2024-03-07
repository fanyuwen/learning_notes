# java protected作用域的一个问题
protected作用域对于不属于同一个包的父子类之间的可访问性有限制
摘自《java程序语言(第三版)》3.5节`protected`的确切含义:
> 1. 跨包的protected实例属性依然可以被子类访问
> 2. 将拥有该protected实例属性的类型(依然是父子类型)作为方法参数传递,如果参数类型是定义该方法的类型或者子类型,则当前方法可以访问参数类型的protected实例属性,如果不是则无法访问(包括父类型)
>    这种限制的原因是: 每个子类都继承了超类的约定,并以某种方式扩展了约定,如果某个子类(作为扩展约束的一部分)对超类的protected实例属性的值进行了某种约束,如果其它不同的子类能够访问这个子类对象的
>    protected实例属性,那么其它子类对象就能以一种破坏约定的方式来操作这个子类对象的实例属性,这显然是不允许的
> 3. 其它情况都能访问(声明为protected的实例属性对包级别的所有代码来说都是可用的,意味着下面的父子类都在一个包下面,protected实例属性都能访问,同一个包下面的类之间被假定为拥有较高的可信度,而不会互相造成约定冲突)
> 4. 对于静态属性: 对于protected静态属性,可以在任何继承类中访问,因为子类不能够修改其静态成员的约定,只能够隐藏它们,而不是覆盖它们,因而,不存在其它类违反约束的危险
>    (我理解父类定义的静态属性,对于所有的子类来说就一个,无论哪个子类进行修改,所有子类包括父类都能看到那个属性的最新值)
```java
    package com.fatherScope;

    import com.sonScope1.Son1;
    import com.sonScope2.Son2;
    //在一个包里定义一个Father类
    public class Father{
        //定义一个
        protected int age;
        //这边以父类型为参数,可以访问protected实例属性
        protected void visit(Father father) {
            System.out.println(father.age);
        }

        //这边以子类类型为参数,也可以访问到protected作用域的实例属性(因为参数类型是定义方法类型的子类型)
        protected void visit(Son1 son1) {
            System.out.println(son1.age);
        }
        //同上
        protected void visit(Son2 son2) {
            System.out.println(son2.age);
        }
    }
```

```java
    package com.sonScope1;
    import com.fatherScope.Father;
    import com.sonScope2.Son2;
    //在一个包里定义一个Father类
    public class Son1 extends Father{
        //在子类中是无法访问父类类型参数的protected实例属性(因为参数类型为定义方法的父类型,则无法访问)
        protected void visit(Father father) {
            System.out.println(father.age);//这边的访问代码会报错(can't access protected property)
        }
        //同上,也不能访问同一个父类下的其它子类的protected实例属性(因为参数类型为定义方法的父类型的其它子类型,则无法访问)
        protected void visit(Son2 son2) {
            System.out.println(son2.age);//这边的访问代码会报错(can't access protected property)
        }
        protected void visit(Father father) {
            super.visit(father);//这边可以通过调用父类的访问方法访问父类参数
        }
        //但是可以访问自己类型参数的protected实例属性
        protected void visit(Son1 son1) {
            System.out.println(son1.age);//这边可以访问自己类型的protected age实例属性
        }
        //依然可以访问从父类继承来的protected实例属性
        protected void visit() {
            System.out.println(age);//但是依然可以访问继承过来的父类的protected实例属性
        }
    }
```

```java
    package com.sonScope2;
    import com.fatherScope.Father;
    //在一个包里定义一个Father类
    public class Son2 extends Father{
        //在子类中是无法访问父类类型参数的protected实例属性
        protected void visit(Father father) {
            System.out.println(father.age);//这边的访问代码会报错(can't access protected property)
        }
        //但是可以访问自己类型参数的protected实例属性
        protected void visit(Son2 son2) {
            System.out.println(son2.age);//这边可以访问自己类型的protected age实例属性
        }
    }
```