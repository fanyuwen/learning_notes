### 关于java枚举的一个点
> 因为java的枚举在启动时就会由虚拟机初始化创建,所以当2个枚举之间互相引用时,本身会有问题,虽然编译不会报错,但是运行时会有一个大坑,后申明的枚举获得的时一个`NULL`,代码如下:
```java
    //在枚举A中引用了枚举B
    enum A{
        AA(B.BB);
        private final B b;

        A(B b) {
            this.b = b;
        }
    }
    //在枚举B中引用了枚举A,但是在实际
    enum B {
        BB(A.AA);
        private final A a;

        B(A a) {
            this.a = a;//这里后申明定义的B的构造函数参数的A类型实际的值是NULL,如果不小心在后续使用中时极有可能发生`NullPointerException`
        }
    }
```
下面是我目前想到的修复方案:
```java
    enum A {
        AA(() -> B.BB);
        private final B b;

        //当然这里参数类型直接使用B也可以
        A(Supplier<B> supplier) {
            this.b = supplier.get();
            this.b.setA(this);
        }
    }

    enum B {
        BB;
        private A a;

        public void setA(A a) {
            this.a = a;
        }
    }
```
