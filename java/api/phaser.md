### Phaser (`java.util.concurrent.Phaser`)

**jdk 1.7新增**

一个可重用的同步屏障,在功能上类似于`CyclicBarrier`和`CountDownLatch`,但支持更灵活的使用.

+ **注册**: 与其他屏障的情况不同,在相位器上注册同步的各方数量可能会随着时间的推移而变化. 任务可以在任何时候注册(
  使用方法`register`,`bulkRegister`,或构造函数的形式来建立初始方数),也可以在任何到达时取消注册(
  使用`arriveAndDeregister`). 与大多数基本同步结构的情况一样,
  注册和注销只影响内部计数,它们不建立任何进一步的内部簿记,因此任务无法查询它们是否已注册.(
  但是,您可以通过子类化这个类来引入这种簿记)
+ **同步**: 就像循环屏障(`CyclicBarrier`)一样,相位器可能会被反复等待. 方法`arriveAndAwaitAdvance`
  的作用类似于`CyclicBarrier.await`. 相位器的每一代具有相关联的相位(`phase`)数.
  相位数从0开始,并在所有各方到达相位器时前进,在达到`Integer.MAX_VALUE`后重新回到0.
  相位号的使用可以通过两种方法独立控制到达相位器和等待其他相位器时的动作,这两种方法可以由任何注册方调用:
    + `Arrival`. 方法`arrive`和`arriveAndDeregister`
      记录到达,这些方法不会阻塞,而是返回一个相关联的到达相位号,也就是说,接收到的相位器的相位数.当给定阶段的最后一方到达时,将执行一个可选操作,并继续该阶段.这些操作由触发阶段推进的一方执行,并通过重写方法`onAdvance(int, int)`
      来安排,
      该方法也控制终止.重写此方法类似于为`CyclicBarrier`提供屏障操作(`barrier action`),但比提供屏障操作更灵活.
    + `Waiting`. 方法`awaitAdvance`需要一个参数来指示到达阶段(`phase`)号,并在相位器前进到(或已经处于)
      另一个阶段时返回.与使用`CyclicBarrier`的类似结构不同,即使等待线程被中断,方法`awaitAdvance`也会继续等待.
      可中断和超时版本也可用,但在任务可中断等待或超时等待时遇到的异常不会改变相位器的状态.
      如果有必要,您可以在这些异常的处理程序中执行任何相关的恢复,通常是在调用`forceTermination`之后.`Phasers`
      也可以被在`ForkJoinPool`里执行的任务使用,,这将确保在其他任务被阻塞等待阶段推进时执行任务的足够并行性.
+ **终止**: 相位器可以进入终止状态,该状态可以使用方法`isTerminated`进行检查. 在终止时,所有同步方法立即返回,而不需要等待提前,正如一个负返回值所指示的那样.
  同样,在终止后进行注册的尝试也没有效果. 当调用`onAdvance`返回`true`时触发终止. 如果取消注册导致注册方的数量变为零,
  则默认实现返回`true`.
  如下所示,
  当相位器用固定次数的迭代控制动作时,通常可以方便地覆盖此方法,以便在当前相位数达到阈值时导致终止.方法`forceTermination`
  也可用于突然释放等待线程并允许它们终止.
+ **分层布置**: 相位器可以分层(即,在树结构中构造)以减少争用. 具有大量参与方的相位器可能会经历沉重的同步争用成本,
  因此可以将其设置为子相位器组共享一个共同的父节点. 这可能会大大提高吞吐量，尽管它会导致更大的每次操作开销.
  在分层相位器的树中,子相位器与其父相位器的注册和注销是自动管理的. 每当子相位器的已注册方的数量变为非零(
  如在`phaser (phaser, int)`构造函数、`register`或`bulkRegister`中所建立的)时,子相位器将与其父相位器注册.
  每当由于调用`arriveAndDeregister`而注册的参与方数量变为零时, 子阶段将从其父阶段注销注册.
+ **监控**: 虽然同步方法只能由注册方调用,但相位器的当前状态可以由任何调用方监视.
  在任何给定时刻,总共有`getRegisteredParties`参与方,其中`getArrivedParties`已到达当前阶段(`getPhase`).
  当剩余的(`getUnarrivedParties`)参与方到达时,该阶段继续进行. 这些方法返回的值可能反映瞬时状态,
  因此通常对同步控制不太有用. 方法`toString`以一种便于非正式监视的形式返回这些状态查询的快照.

实现是通过一个long类型的state来对以上数据进行统计和管理

主状态表示,持有四个位域: 未到达的——尚未到达屏障的各方数量
(比特0-15)——等待的各方数量
(比特16-31)阶段——屏障的生成
(比特32-62)终止——如果屏障终止
(比特63 /符号)
除了没有注册各方的相位器被区分为具有零方和一个未到达方的非法状态(下面编码为EMPTY)
.为了有效地维护原子性,这些值被打包到一个(原子)长文件中. 良好的性能依赖于保持状态解码和编码简单,以及保持竞争窗口短.
所有状态更新都通过CAS执行,除了子相位器(即具有非空父级的相位器)的初始注册. 在这种(相对罕见的)情况下,
我们使用内置同步在首次向其父进程注册时进行锁定. 允许子相位器的相位滞后于它的祖先相位,
直到它被实际访问——参见方法`reconcileState`.

```java
    private volatile long state;//通过一个long类型的state来进行管理,这个已经不是第一次了Doug Lea大师就喜欢这么玩

private static final int MAX_PARTIES = 0xffff;//获取parties的掩码
private static final int MAX_PHASE = Integer.MAX_VALUE;//最大阶段数量其实就是32位数的最大值
private static final int PARTIES_SHIFT = 16;//parties的移位量
private static final int PHASE_SHIFT = 32;//phase(阶段)的移位量
private static final int UNARRIVED_MASK = 0xffff;      // to mask ints 未到达的部分的数量
private static final long PARTIES_MASK = 0xffff0000L; // to mask longs 这个是配置的部分的总量(构造函数初始化或者register/bulkRegister方法注册),用来到达一个阶段的时候恢复parties部分
private static final long COUNTS_MASK = 0xffffffffL; //
private static final long TERMINATION_BIT = 1L << 63; //最高位为状态位

// some special values
private static final int ONE_ARRIVAL = 1; //每次部分到达时,parties递增的数量
private static final int ONE_PARTY = 1 << PARTIES_SHIFT; //新增一个parties参与者时需要更新的值
private static final int ONE_DEREGISTER = ONE_ARRIVAL | ONE_PARTY; //注册一个新的parties,state需要加的值
private static final int EMPTY = 1;//初始的空的parties
```

Example:

+ 相位器可以代替`CountDownLatch`来控制一个一次性动作,服务于可变数量的各方.
  典型的习惯用法是将此设置为首先注册,然后启动操作,然后取消注册,如下所示:

```java
void runTasks(List<Runnable> tasks) {
    final Phaser phaser = new Phaser(1);// "1" to register self    
    // create and start threads    
    for (final Runnable task : tasks) {
        phaser.register();//每一个任务新注册一个parties      
        new Thread() {
            public void run() {
                phaser.arriveAndAwaitAdvance();// await all creation          
                task.run();
            }
        }.start();
    }
    // allow threads to start and deregister self    
    phaser.arriveAndDeregister();
}
```

+ 导致一组线程在给定次数的迭代中重复执行动作的一种方法是重写onAdvance:

```java
void startTasks(List<Runnable> tasks, final int iterations) {
    final Phaser phaser = new Phaser() {
        protected boolean onAdvance(int phase, int registeredParties) {
            return phase >= iterations || registeredParties == 0;
        }
    };
    phaser.register();
    for (final Runnable task : tasks) {
        phaser.register();
        new Thread() {
            public void run() {
                do {
                    task.run();
                    phaser.arriveAndAwaitAdvance();
                } while (!phaser.isTerminated());
            }
        }.start();
    }
    phaser.arriveAndDeregister(); // deregister self, don't wait  
}
```

+ 如果主任务必须等待终止,它可以重新注册,然后执行类似的循环:
```java
public void waitTerminated() {
    phaser.register();
    while (!phaser.isTerminated()) 
        phaser.arriveAndAwaitAdvance();
}
```

+ 相关的结构可以用于等待特定的阶段号,在您确定阶段永远不会围绕Integer.MAX_VALUE的上下文中. 例如:
```java
void awaitPhase(Phaser phaser, int phase) {
    int p = phaser.register(); // assumes caller not already registered    
    while (p < phase) {
        if (phaser.isTerminated()) {
            // ... deal with unexpected termination
        } else
            p = phaser.arriveAndAwaitAdvance();
    }
    phaser.arriveAndDeregister();
}
```

+ 要使用相位器树创建一组n个任务,可以使用以下形式的代码,假设Task类具有一个构造函数,该构造函数接受它在构造时注册的相位器.
  在调用build(new Task[n], 0, n, new Phaser())之后,这些任务可以启动,例如通过提交到池:
```java
void build(Task[] tasks, int lo, int hi, Phaser ph) {
    if (hi - lo > TASKS_PER_PHASER) {
        for (int i = lo; i < hi; i += TASKS_PER_PHASER) {
            int j = Math.min(i + TASKS_PER_PHASER, hi);
            build(tasks, i, j, new Phaser(ph));
        }
    } else {
        for (int i = lo; i < hi; ++i)
            tasks[i] = new Task(ph);
            // assumes new Task(ph) performs ph.register() 
    }  
}
```

+ `TASKS_PER_PHASER`的最佳值主要取决于预期的同步速率. 低至4的值可能适合于非常小的每阶段任务体(因此是高速率),或者高达数百的值适合于非常大的任务体.
  实现说明:此实现将最大参与方数限制为65535。尝试注册其他方会导致IllegalStateException.但是,您可以并且应该创建分层相位器来容纳任意大的参与者集.