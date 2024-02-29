## 什么场景下的循环依赖Spring无法解决?
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
 ```java
    /**
     * 把A改为单例,Spring容器启动过程中,对象A会先被创建,对象B创建过程中,因为属性A是单例的,所以是可以通过三级缓存完成注入的
     * 所以这种循环依赖问题,Spring是可以解决的
     */
    //单例bean对象A
    @Component
//    @Scope("prototype")
    public class A{
      @Autowired
      private B b;
    }
    //原型bean对象B
    @Component
    @Scope("prototype")
    public class B{
      @Autowired
      private A a;
    }
 ``` 
  如果对象A被定义为原型,对象B被定义为单例,它们之间的循环依赖问题,Spring也是可以解决的,无非是多绕几个圈子而已
  一句话总结***除非相互依赖的bean都是原型bean,否则,Spring是可以解决循环依赖的***
 ```java
    /**
     * 把B改为单例,这种情况稍微复杂一点
     * Spring容器启动过程中,对象A会先被创建,需要注入属性B,所以创建对象B,对象B的创建过程中,属性A是原型bean,会去重新创建
     * 一个新的bean,需要注入属性B,所以去创建对象B,因为B是单例的,所以是通过三级缓存完成注入的,所以这种循环依赖问题,Spring也是可以解决的
     */
    //原型bean对象A
    @Component
    @Scope("prototype")
    public class A{
      @Autowired
      private B b;
    }
    //单例bean对象B
    @Component
//    @Scope("prototype")
    public class B{
      @Autowired
      private A a;
    }
 ```
+ 采用`@DependsOn`注解而导致的循环依赖
  `@DependsOn`注解主要用于指定当前bean对象所依赖的其它bean对象,Spring在创建当前bean之前,会先创建由`@DependsOn`注解
  所指定的依赖的bean对象
  如下代码实例,bean对象A在创建之前,bean对象B要先完成创建;同时bean对象B创建之前,bean对象A也要先完成创建,这明显是矛盾的,
  Spring无法解决,唯一的解决办法就是找到`@DependsOn`注解循环依赖的地方,迫使它不循环依赖
  ```java
    @Component
    @DependsOn("b")
    public class A{}
    @Component
    @DependsOn("a")
    public class B{}
  ```
  + 采用`@Async`注解
    ```java
      /**
       * 先创建对象A,后依赖创建对象B
       * 在对象A中,使用了@Async,这种情况下引发的循环依赖问题,Spring是无法解决的
       */
      @EnableAsync
      public class Config{}
      @Component
      public class A{
          @Autowired
          private B b;
          @Async
          public void test(){
              System.out.println(b);
          }
      }
      @Component
      public class B{
          @Autowired
          private A a;
      }
    ``` 
    原因在于Spring源码里,`AbstractAutowireCapableBeanFactory#doCreateBean()`方法中有如下代码
    ```java
      protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
              throws BeanCreationException {
          // ...(省略代码)
          // Initialize the bean instance.
          Object exposedObject = bean;
          try {
              populateBean(beanName, mbd, instanceWrapper);
              //初始化
              exposedObject = initializeBean(beanName, exposedObject, mbd);
          }catch (Throwable ex) {
              //...
          }
          if (earlySingletonExposure) {
              Object earlySingletonReference = getSingleton(beanName, false);
              if (earlySingletonReference != null) {
                  if (exposedObject == bean) {
                      exposedObject = earlySingletonReference;
                  }
                  else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
                      //...
                      //当出现循环依赖,使用@Async注解抛出的异常判断如下
                      if (!actualDependentBeans.isEmpty()) {
                          throw new BeanCurrentlyInCreationException(beanName,
                                  "Bean with name '" + beanName + "' has been injected into other beans [" +
                                  StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +
                                  "] in its raw version as part of a circular reference, but has eventually been " +
                                  "wrapped. This means that said other beans do not use the final version of the " +
                                  "bean. This is often the result of over-eager type matching - consider using " +
                                  "'getBeanNamesForType' with the 'allowEagerInit' flag turned off, for example.");
                      }
                  }
              }
          }
      }
    ``` 
    引发抛异常的原因(简单说明):在对象A的初始化后阶段,会遍历所有的后置处理器,并执行它们的初始化后的方法,其中,需要提到的第一个后置处理器是
    `AnnotationAwareAspectJAutoProxyCreator`,它主要用于处理AOP,如果存在循环依赖,该后置处理器会直接返回对象A的原始对象,不管对象
    A是否需要进行AOP,假设后续没有其它后置处理器对这个原始对象进行修改,对象A就会去二级缓存中取出半成品对象作为最终的返回对象,此时我们可以
    理解为对象A和对象B属性填充阶段从三级缓存中执行Lambda表达式后注入的对象A是同一个对象,这是正常情况
    但是,正是因为使用了`@Async`注解,所以Spring在对象A的初始化后阶段,还会执行一个我们不得不提的后置处理器,它的名字是:**`AsyncAnnotationBeanPostProcessor`**
    该后置处理器就会修改对象A的返回结果为另外一个代理对象,那么问题来了,此时对象B注入的对象A和这个修改后的对象A就不是同一个对象了,所以,此时Spring
    就无法通过三级缓存来解决这个问题了,
    其实,也并不是说出现了循环依赖问题时,使用`@Async`注解,Spring就一定无法解决,比如如下案例,在对象B中,使用了`@Async`注解,这种场景的循环依赖问题,Spring是可以解决的
    ```java
        /**
         * 依然是先创建对象A,后依赖创建对象B
         * 在对象B中,使用了@Async,这种情况的循环依赖,Spring是可以解决的
         * 因为对象A中注入的对象B是经历了完整创建过程的,它是AsyncAnnotationBeanPostProcessor
         * 后置处理器执行后的对象,所以这种情形下使用@Async,对于Spring解决循环依赖问题没有影响
         */
        @Component
        public class A{
            @Autowired
            private B b;
        }
        @Component
        public class B{
            @Autowired
            private A a;
            @Async
            public void test(){
                System.out.println(b);
            }
        }
    ```
+ 解决方案:
  1. 使用`@Lazy`注解 
    ```java
        @Component
        public class A{
            @Autowired
            @Lazy
            private B b;
            @Async
            public void test(){
                System.out.println(b);
            }
        }
    ``` 
  2. 使用`@DependsOn`注解指定加载的先后顺序,即使用`@Async`修饰的类后加载 
  ```java
    @Component
    @DependsOn("b")
    public class A{
        @Autowired
        private B b;
        @Async
        public void test(){
            System.out.println(b);
        }
    }
  ```
  