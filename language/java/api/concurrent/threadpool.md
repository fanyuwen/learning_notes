### ThreadPoolExecutor

+ > 通过`execute`方法提交的任务,当任务在执行的过程当中有抛出异常,`execute`方法本身会捕获,但是也就是会调用钩子方法`afterExecute`(可进行重写),
  > 实际上还是会抛出异常并不会进行处理,然后当前线程不会再从任务队列里获取任务,而是执行退出线程池需要的工作(如果线程池的核心线程不够会启动新的线程)然后安全的退出,
  > 然后当前线程就结束了,也可以通过`Thread.setDefaultUncaughtExceptionHandler`方法设置全局线程默认异常处理(每个`Thread`也可以通过`setUncaughtExceptionHandler`单独设置自己的异常处理),

### ScheduledThreadPoolExecutor

+ > 继承自`ThreadPoolExecutor`新增了`continueExistingPeriodicTasksAfterShutdown`、`executeExistingDelayedTasksAfterShutdown`和`removeOnCancel`
  > + `continueExistingPeriodicTasksAfterShutdown`:默认值`false`,当线程池关闭(`shutdown()`)的时候,是否继续保留留在队列里的周期性任务,所以默认是不保留,会从等待队列里删除掉
  > + `executeExistingDelayedTasksAfterShutdown`:默认值`true`,当线程池关闭(`shutdown()`)的时候,是否继续保留留在队列里的延迟性任务,所以默认是保留的,不会从等待队列里删除掉
  > + `removeOnCancel`:默认值`false`,取消(`cancel(boolean mayInterruptIfRunning)`)当前任务的时候如果取消成功,则是否需要从队列中删除当前任务,默认不删除