## LockSupport和CountDownLatch

#### 1.LockSupport简介

- AQS实现依赖两个类：Unsafe(提供CAS操作)和LockSupport(提供park/unpark操作)
- LockSupport用于创建锁和其他同步类的基本线程阻塞原语：当调用LockSupport.park时，表示当前线程将会等待，直至获得许可，当调用LockSupport.unpark时，必须把等待获得许可的线程作为参数进行传递，好让此线程继续运行。
- LockSupport依赖Unsafe实现

#### 2.Thread.sleep()和LockSupport.park()的区别

- 从功能上说，两则都是阻塞当线程的执行，且都不会释放当前线程占有的锁资源
- Thread.sleep()没法从外部唤醒，只能自己醒过来，LockSupport.park()方法可以被另一个线程调用LockSupport.unpark()方法唤醒
- Thread.sleep()方法声明上抛出了InterruptedException中断异常，所以调用者需要捕获这个异常或者再抛出，LockSupport.park()方法不需要捕获中断异常

#### 3.Object.wait()和LockSupport.park()的区别

- Object.wait()需要在synchronized块中执行，LockSupport.park()可以在任意地方执行
- Object.wait()方法声明抛出了中断异常，调用者需要捕获或者再抛出，LockSupport.park()不需要捕获中断异常
- Object.wait()不带超时的，需要另一个线程执行notify()来唤醒，但不一定继续执行后续内容，LockSupport.park()不带超时的，需要另一个线程执行unpark()来唤醒，一定会继续执行后续内容
- LockSupport.park()不会释放锁资源，Object.wait()会释放锁资源
- 如果在wait()之前执行了notify()，会抛出异常，如果在park()之前执行了unpark()，线程不会阻塞，直接跳过park()，继续执行后续内容。

#### 4.CountDownLatch简介

依赖AQS实现，CountDownLatch典型的用法是将一个程序分为N个互相独立的可解决任务，并创建值为n的CountDownLatch。当每一个任务完成时，都会在这个锁存器上调用countDown，等待问题被解决的任务调用这个锁存器的await，将他们自己拦住，直至锁存器计数结束。

#### 5.CountDownLatch的使用

- 主要方法：await()和countDown()
- await()会使会使当前线程在锁存器倒计数至零之前一直等待，除非线程被中断
- countDown()会将递减锁存器的计数，如果计数到达零，则释放所有等待的线程

