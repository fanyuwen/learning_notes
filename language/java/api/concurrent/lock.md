### 为什么aqs要采用双端队列进行等待线程的存储
> 双向链表有两个指针,前驱指针和后驱指针,所以双向链表能保证在插入和删除节点的时候要比单向链表更加简单和高效
> 1. 当一个没有竞争到锁的线程需要等待获取锁的过程当中加入到阻塞队列并且阻塞等待的一个前提是,当前等待节点的前置节点是一个正常状态(waitStatus是默认)
>    这样设计的目的是为了避免存在异常状态的节点,导致无法唤醒后续的节点线程,所以线程在阻塞之前需要判断前置节点的状态,如果没有前置节点的引用,那么
>    就需要从头节点开始遍历,这个效率是很低的
> 2. 在AbstractQueuedSynchronizer里有一个可以响应中断的获取锁的方式,它表示处于阻塞等待获取锁的线程是允许被中断的,也就是没有获取到锁的线程
>    在加入到同步队列之后是可以被外部的线程通过interrupt方法去唤醒和中断的,但是这个被中断唤醒的线程节点的waitStatus会被更新为cancelled状态
>    他是不需要去竞争锁的,但是它仍然存在于双向链表里面,意味着后续的锁的竞争需要把这个节点从链表中移除,否则会导致阻塞等待的线程没法被正常唤醒,这种
>    情况如果是单向链表,就需要从头节点开始遍历,找到并移除异常状态的节点,这种效率是很低的,而且还会又锁唤醒的操作和遍历操作之间的竞争
> 3. 为了避免线程阻塞和唤醒的开销,新加入链表的线程会首先通过自旋的方式去尝试竞争锁,但是实际上按照公平锁的设计,只有头节点的下一个节点才有必要去竞争锁
>    后续的节点去竞争锁的意义不大,否则会造成**惊群效应**(大量的线程去竞争锁,最终只有一个线程能获取到,这会带来比较大的性能开销),所以为了避免这个问题
>    加入到链表里面的节点在尝试竞争锁的时候,需要去判断前置节点是不是头节点,如果不是头节点就没有必要去触发锁的竞争动作,所以会设计前置节点的查找,如果是
>    单向链表是无法实现这样的功能的

### LockSupport与Object.wait/notify,Condition.await/signal的区别
> 今天也是在看aqs(`AbstractQueuedSynchronizer`)的源码的时候发现的,其实已经就注意了,只是这次在研究共享(share)模式的时候额外注意了一下,`Object.wait/Condition.await`等待
> 与对应的`Object.notify/Condition.signal`唤醒之间是有严格的先后关系的,可以参考java语言规范里*happen-before*原则,理论上`Object.wait/Condition.await` happen before
> `Object.notify/Condition.signal`,所以notify或者signal只能唤醒之气wait或者await的线程,而不能唤醒之后的,而`LockSupport`则不一样,他是有一个被称为许可(permit)的条件,是一个整数值,
> 也代表一种状态,当调用park系方法时,设置该许可为1并挂起当前线程,调用unpark系方法时会将该许可重新设置为0,当许可数为0时表示线程可以正常运行

关于`LockSupport`的使用遇到的一个问题,对于在java里的代码API调用中阻塞的逻辑大部分都是通过调用`LockSupport`的`park`方法进行阻塞的,所以在自己的代码里尤其是是直接使用`LockSupport`时,需要小心
因为有可能会影响你使用的其它的并发工具API,下面是一个例子
```java
        CountDownLatch countDownLatch = new CountDownLatch(1);
        Thread t = new Thread(() -> {
            //这里通过CountDownLatch的wait等待,底层走的时AQS,最终也会调用LockSupport的park
            try {
                countDownLatch.await();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            //这里再手动调用LockSupport的park方法
            LockSupport.park();
        });
        t.start();
        //这里的睡眠时为了让上面的线程先执行并且先阻塞
        //描述一下如果不加这段睡眠的影响,可能是当前main线程会先执行,调用unpark(给t线程设置一个许可),然后调用countDownLatch.countDown()将countDownLatch的计数减为0,然后t线程运行,在await方法调用的地方因为已经将计数减为0,所以不需要阻塞
        //直接往下运行,这个时候因为已经有一个许可了,所以park也是消耗掉这个许可直接运行完,整个程序不会阻塞
        //还有另一种情况,就是当t线程先运行,阻塞在await调用的地方,然后main线程运行,先调用unpark方法唤醒t线程,然后调用countDownLatch.countDown方法,只是在该方法里执行完计数设置为0并调用LockSupport.unpark指定的t线程,此时时间片切换到t线程
        //从await中恢复(因为显式调用LockSupport.unpark),然后检查计数已经满足直接往下执行,到执行park方法时,因为又设置了一次许可(countDownLatch.countDown方法里的调用),所以也直接消耗许可并不会阻塞,执行完毕
        TimeUnit.MILLISECONDS.sleep(50);
        //注意这里的unpark会意外的唤醒阻塞api里的park调用,也就是会唤醒上面线程里的countDownLatch.await()阻塞调用,但虽然唤醒了,内部是一个循环,还是会检查阻塞条件是否满足,不满足还是会阻塞,但是会消耗掉这次unpark设置的许可
        LockSupport.unpark(t);
        //这里的countDown会修改阻塞条件并调用LockSupport的unpark方法恢复线程执行,所以会再次恢复上面的countDownLatch.await()调用,并且能够执行完,顺利地到达LockSupport的park调用,但此时会一直卡着,因为没有许可设置了
        countDownLatch.countDown();
```

今天还看到java.lang.Thread类的join方法的doc描述,原来它是通过在自身Thread对象上通过调用Object的wait方法进行阻塞,所以它在最后说了建议不要用Thread对象来作为wait或者notify或者notifyAll方法的调用实例,因为线程执行完毕(无论是正常结束还是异常)
都会调用自身的notify或者notifyAll方法,所以如果你调用了线程对象上的wait或者notify或者notifyAll有可能会意外的唤醒join方法或者被线程执行完的notify意外地唤醒其它地方的wait
