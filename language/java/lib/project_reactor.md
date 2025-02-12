## Spring的响应式编程类库 project reactor的api学习

```java
import reactor.core.publisher.Flux;

void subscribeLearn(){
    //生成flux同步调用
    Flux.generate(synchronousSink -> {
        synchronousSink.next();//每次回调只能调用一次next方法传递参数,但是可以再调用一次error或者complete
    });
}
```