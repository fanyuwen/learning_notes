## Spring的响应式编程类库 project reactor的api学习

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

+ 另一个重载方法在上面方法的基础上再加一个*Consumer*类型的参数,用来在结束的时候对最后的状态对象做处理