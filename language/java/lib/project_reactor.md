## Spring的响应式编程类库 project reactor的api学习

1. **generate** 
   + *generate*默认有一个参数是*SynchronousSink*类型的*Consumer*,可以调用它的*next*方法传输值,或者调用终止方法*complete*或者*error*(二者只能调用一个)

       ```java
       import reactor.core.publisher.Flux;
    
       void subscribeLearn(){
           //生成flux同步调用
           Flux.generate(synchronousSink -> {
               synchronousSink.next();//每次回调只能调用一次next方法传递参数,但是可以再调用一次error或者complete(error和complete只能调用一个)
               synchronousSink.error(new RuntimeException());
               synchronousSink.complete();
           });
       }
       ```

   + 另一个重载方法需要提供一个状态对象初始值,然后是一个该值和*SynchronousSink*的*BiFunction*,需要再返回一个初始值类型的返回值

        ```java
        import reactor.core.publisher.Flux;
        import java.util.concurrent.atomic.AtomicLong;
        
        /**
         * 这是一个以整数为例子
         */
        void subscribeLeanGenerate() {
            Flux.generate(() -> 1, (i, synchronousSink) -> {
                synchronousSink.next(i);
                synchronousSink.complete();
                synchronousSink.error(new RuntimeException());
                return i + 1;
            });
        }
        
        /**
         * 还可以可变对象为例子(参考官网的例子)
         */
        void subscribeLeanGenerate1() {
            Flux.generate(AtomicLong::new, ((atomicLong, synchronousSink) -> {
                long num = atomicLong.getAndIncrement();
                synchronousSink.next(num);
                if(num == 10){
                    synchronousSink.complete();
                }
                return atomicLong;
            }));
        }
        
        ```

   + 另一个重载方法在上面方法的基础上再加一个*Consumer*类型的参数,用来在结束的时候对最后的状态对象做处理,如果你的对象需要进行清理,可以使用这个重载方法,如果状态包含需要在流程结束时处理的数据库连接或其他资源，则Consumer lambda可以关闭连接或以其他方式处理应该在流程结束时完成的任何任务

        ```java
        import reactor.core.publisher.Flux;
        import java.util.concurrent.atomic.AtomicLong;
        
        /**
         * 官网例子
         */
        void subscribeLeanGenerate2() {
            Flux.generate(AtomicLong::new, (state, sink) -> {
                long i = state.getAndIncrement();
                sink.next("3 x" + i + " = " + 3 * i);
                if(i == 10) sink.complete();
                return state;
            }, state -> System.out.println("state: " + state));
        }
        ```

2. **create**
    + *create*是*Flux*的一种更高级的程序化创建形式,它适用于每轮多次发射,甚至来自多个线程.它公开了*FluxSink*及其*next*、*error*和*complete*方法.与*generate*相反,它没有基于状态的变体.另一方面，它可以在回调中触发多线程事件
      > create不会使代码并行化,也不会使代码异步化,尽管它可以与异步api一起使用.如果在create lambda中阻塞,就会使自己暴露于死锁和类似的副作用.即使使用subscribeOn,也有一个警告,即长阻塞的create lambda(例如调用sink.next(t)的无限循环)可能会锁定管道:由于循环占用了它们应该运行的同一个线程,因此请求(request方法)永远不会被执行,
      > 使用subscribeOn(Scheduler, false)变体:requestOnSeparateThread = false将使用Scheduler线程进行创建,并且仍然通过在原始线程中执行请求来让数据流动.