#   JAVA中的并发工具类

##  数据结构

###  等待多线程完成的CountDownLatch

需求出发点：我们需要解析一个Excel里多个sheet的数据，此时可以考虑使用多线程，每个线程解析一个sheet里的数据，等到所有的sheet都解析完之后，程序需要提示解析完成（或者汇总结果）在这个需求中，要实现主线程等待所有线程完成sheet的解析操作，最简单的做法是使用join()方法

我们调用CountDownLatch的countDown方法时,N就会减1,CountDownLatch的await方法会阻塞当前线程,直到N变为零(也就是线程都执行完了),由于countDown方法可以用在任何地方

CountDownLatch和join区别：
>*相同点:都能等待一个或者多个线程执行完成操作,比如等待三个线程执行完毕后,第四个线程才能执行
>*不同点:join能让线程按我们预想的的顺序执行,比如线程1执行完了,线程2才能执行,线程2执行完,线程3才能执行,但是CountDownLatch就做不到.



###  # 同步屏障CyclicBarrier

CyclicBarrier默认的构造方法是CyclicBarrier（int parties），其参数表示屏障拦截的线程数量，每个线程调用await方法告诉CyclicBarrier我已经到达了屏障，然后当前线程被阻塞 

```java

```


区别：CountDownLatch的计数器只能使用一次，而CyclicBarrier的计数器可以使用reset()方法重置。所以CyclicBarrier能处理更为复杂的业务场景。例如，如果计算发生错误，可以重置计数器，并让线程重新执行一次。
CyclicBarrier还提供其他有用的方法，比如getNumberWaiting方法可以获得Cyclic-Barrier阻塞的线程数量。isBroken()方法用来了解阻塞的线程是否被中断。


###  控制并发线程数的Semaphore
Semaphore（信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。（类似于红绿灯）

Semaphore还提供一些其他方法，具体如下。
>* int **availablePermits**()：返回此信号量中当前可用的许可证数。
>* int **getQueueLength**()：返回正在等待获取许可证的线程数。
>* boolean **hasQueuedThreads**()：是否有线程正在等待获取许可证。
>* void **reducePermits**（int reduction）：减少reduction个许可证，是个protected方法。
>* Collection **getQueuedThreads**()：返回所有等待获取许可证的线程集合，是个protected方法。

###  线程间交换数据的Exchanger
Exchanger用于进行线程间的数据交换。它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。这两个线程通过exchange方法交换数据，如果第一个线程先执行exchange()方法，它会一直等待第二个线程也执行exchange方法，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方。







