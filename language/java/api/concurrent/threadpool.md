### ThreadPoolExecutor

+ > 通过`execute`方法提交的任务,当任务在执行的过程当中有抛出异常,`execute`方法本身会捕获,但是也就是会调用钩子方法`afterExecute`(可进行重写),
  > 实际上还是会抛出异常并不会进行处理,然后当前线程不会再从任务队列里获取任务,而是执行退出线程池需要的工作(如果线程池的核心线程不够会启动新的线程)然后安全的退出,
  > 然后当前线程就结束了,也可以通过`Thread.setDefaultUncaughtExceptionHandler`方法设置全局线程默认异常处理(每个`Thread`对象也可以通过`setUncaughtExceptionHandler`单独设置当前线程的异常处理),

+ > 下面这段代码是多线程使的一个误解(从一个视频网站里看到的),给定一个线程池,核心线程数和最大线程数相等,然后给定一个固定容量的阻塞队列,接着循环10次,每次往线程池里提交多个任务,通过`CountDownLatch`计数器进行等待这个批次的任务执行完,才会  
  >  循环到下一个批次,理论上会等待上一个批次的任务结束才会执行下一个批次的任务,但是在实际执行的过程中会有很大概率抛出拒绝策略异常
  > ```java
  > public static void main() {        
  >      ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(64, 64, 
  >                  1, TimeUnit.DAYS, blockingQueue);
  >      try {
  >         for (int i = 0; i < 10; i++) {
  >               final CountDownLatch countDownLatch = new CountDownLatch(34);
  >               for (int j = 0; j < 34; j++) {
  >                   threadPoolExecutor.execute(() -> {
  >                    try {
  >                          TimeUnit.MILLISECONDS.sleep(500);
  >                     } catch (InterruptedException e) {
  >                          e.printStackTrace();
  >                     } finally {
  >                         countDownLatch.countDown();
  >                     }
  >                   });
  >               }
  >               try {
  >                   countDownLatch.await();
  >               } catch (InterruptedException e) {
  >                   e.printStackTrace();
  >               }
  >               System.out.println("第" + (i + 1) + "个批次执行完毕.");
  >           }
  >           System.out.println("全部执行完毕");
  >      } finally{
  >           threadPoolExecutor.shutdown();
  >      }
  >  }
  > ```

### ScheduledThreadPoolExecutor

+ > 继承自`ThreadPoolExecutor`新增了`continueExistingPeriodicTasksAfterShutdown`、`executeExistingDelayedTasksAfterShutdown`和`removeOnCancel`配置属性
  > + `continueExistingPeriodicTasksAfterShutdown`:默认值`false`,当线程池关闭(`shutdown()`)的时候,是否继续保留留在队列里的周期性任务,所以默认是不保留,会从等待队列里删除掉,以及在正常执行的时候判断如果已经是停止的情况是否还需要执行
  > + `executeExistingDelayedTasksAfterShutdown`:默认值`true`,当线程池关闭(`shutdown()`)的时候,是否继续保留留在队列里的延迟性任务,所以默认是保留的,不会从等待队列里删除掉,以及在正常执行的时候判断如果已经是停止的情况是否还需要执行
  > + `removeOnCancel`:默认值`false`,取消(`cancel(boolean mayInterruptIfRunning)`)当前任务的时候如果取消成功,则是否需要从队列中删除当前任务,默认不删除