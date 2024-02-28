##什么场景下的循环依赖Spring无法解决?
> 我们知道Spring通过三级缓存是可以解决循环依赖问题的,但是,并不是所有的循环依赖问题,Spring都可以通过三级缓存来解决
> 比如,以下几种场景的循环依赖问题,Spring就是无法解决的(定义两个bean A和B A依赖了B,B同时也依赖了A,两者构成了循环依赖):
+ 采用了构造器的注入方式
   <p style="color: blue">对象A实例化的过程中,需要B对象,而B对象的实例化又需要A对象,这就会造成两者都无法完成实例化,实例化都完成不了,更不用谈生成半成品对象了,所以Spring是无法解决的</p>代码如下:
 ```java
        @Component
        public class A{
           private final B b;
           public A(B b){
               this.b = b;
           }
        }
        @Component
        public class B{
            private final A a;
            public B(A a){
               this.a = a;
            }
        }
 ```
   <p style="color: blueviolet">但是,也不是说只要使用构造方法注入出现了循环依赖问题,Spring就一定无法解决,比如对象A中使用了字段注入,对象B中使用了构造方法注入,这种场景,Spring可以解决</p>代码如下: 

  ```java
     /**
      * Spring容器启动过程中,对象A会先被创建,因为采用了属性注入,
      * 所以可以生成半成品对象,这种形式的循环依赖问题,Spring是可以解决的
      */
     @Component
     public class A{
         @Autowired
         private B b;
     }
     @Component
     public class B{
         private final A a;
         public B(A a){
             this.a = a;
         }
     }
  ``` 
   <p style="color: blueviolet">而对象A中使用了构造方法注入,对象B中使用了字段注入,这种场景下,Spring则是无法解决的</p>代码如下:

   ```java
     /**
      * Spring容器启动过程中,对象A会先被创建,因为采用了构造方法注入,
      * 所以不会生成半成品对象,即这种形式的循环依赖问题,Spring也是无法解决的
      */
     @Component
     public class A{ 
        private final B b;
        public A(B b){
            this.b = b;
        }
     }
     @Component
     public class B{
         @Autowired
         private A a;
     }
   ```

+ 相互依赖的bean都是原型bean 
  ```java
     /**
      * 如下A/B 都被定义为了原型bean,它们之间形成的循环依赖问题,Spring是无法解决的
      */
     @Component
     @Scope("prototype")
     public class A{
        @Autowired
        private B b;
     }
     @Component
     @Scope("prototype")
     public class B{
         @Autowired
         private A a;
     }
  ```
  <p style="color: blue">原因分析:首先A被创建,先实例化,属性填充注入对象B,接下来获取B的bean对象,因为对象B是原型bean,所以是直接创建,创建过程中需要注入对象A,因为对象A也是原型bean,所以也会直接
  创建一个A的bean对象,而创建A的bean对象,就又需要注入对象B,这样就形成了一个死循环...所以说,如果相互依赖的bean都是原型bean,Spring是无法解决循环依赖的问题的</p>
  但也不是说只要存在原型bean循环依赖问题,就一定无法解决,比如对象A被定义为单例bean,对象B被定义为原型bean,它们之间的循环依赖问题,Spring是可以解决的
  
+ 


[//]: # ( 3. 采用`@DependsOn`注解而导致的循环依赖)
[//]: # ( 4. 采用`@Async`注解)