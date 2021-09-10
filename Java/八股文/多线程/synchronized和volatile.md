## synchronized和volatile八股文

#### 1.对synchronized关键字的了解

- synchronized关键字是为了解决多线程同步访问共享资源的，即解决线程安全问题
- JDK6之前synchronized是重量级锁，依赖于操作系统的Mutex Lock来实现的，Java 的线程是映射到操作系统的原⽣线程之上的，操作系统实现线程之间的切换需要从用户态转到内核态，这个转换时间成本较高。
- JDK6之后从JVM层面对synchronized进行了优化，**在jdk1.6中对锁的实现引入了大量的优化，如锁粗化(Lock Coarsening)、锁消除(Lock Elimination)、轻量级锁(Lightweight Locking)、偏向锁(Biased Locking)、适应性自旋(Adaptive Spinning)等技术来减少锁操作的开销**。

#### 2.synchronized的原理

- 加锁和释放锁的原理：在字节码层面使用 monitorenter 和 monitorexit 指令，每一个对象在同一时间只与一个monitor(锁)相关联，而一个monitor在同一时间只能被一个线程获得
- 可重入原理：每个对象拥有一个monitor计数器，当线程获取该对象锁后，计数器就会加一，释放锁后就会将计数器减一。
- 保证可见性原理：Java内存模型和happens-before原则
- `synchronized` 修饰的方法并没有 `monitorenter` 指令和 `monitorexit` 指令，取得代之的确实是 `ACC_SYNCHRONIZED` 标识，该标识指明了该方法是一个同步方法。

#### 3.synchronized关键字使用方法

- 修饰代码块：对象锁
- 修饰实例方法：对象锁
- 修饰静态方法：类锁

#### 4.构造方法能用synchronized关键字修饰吗？

- **构造方法不能使用 synchronized 关键字修饰**，构造方法本身就属于线程安全的，不存在同步的构造方法一说。

#### 5.谈谈 synchronized 和 ReentrantLock 的区别

- 都是可重入锁
- synchronized 依赖于 JVM 实现的 而 ReentrantLock 依赖于 API，是 JDK 层面实现的
- ReentrantLock 比 synchronized 增加了一些高级功能
  - **等待可中断**：`ReentrantLock`提供了一种能够中断等待锁的线程的机制，通过 `lock.lockInterruptibly()` 来实现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。
  - **可实现公平锁**：`ReentrantLock`可以指定是公平锁还是非公平锁。而`synchronized`只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。`ReentrantLock`默认情况是非公平的，可以通过 `ReentrantLock`类的`ReentrantLock(boolean fair)`构造方法来制定是否是公平的。
  - **可实现选择性通知（锁可以绑定多个条件）**: `synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制。`ReentrantLock`类当然也可以实现，但是需要借助于`Condition`接口与`newCondition()`方法。

#### 6.synchronized 关键字和volatile关键字区别

- volatile关键字不保证原子性，只保证可见性，而synchronized关键字都保证
- synchronized关键字是为了解决多个线程之间访问资源的同步性，volatile关键字是为了解决变量在多个线程之间的可见性
- volatile关键字只能修饰变量，而synchronized 关键字可以修饰方法和代码块

#### 7.volatile关键字原理

- 保证可见性原理和禁止指令重排序：字节码层面使用了内存屏障相关指令

#### 8.为什么volatile不能保证原子性

- 比如对int类型的变量 i 进行 `i++` 操作，实际上 `i++` 其实是一个复合操作，包括三个原子操作：读取i的值、对i进行加1、将i的值回写到内存。这在多线程环境下，不能保证i的原子性。

（一个变量i被volatile修饰，两个线程想对这个变量修改，都对其进行自增操作也就是i++，i++的过程可以分为三步，首先获取i的值，其次对i的值进行加1，最后将得到的新值写会到缓存中。
 线程A首先得到了i的初始值100，但是还没来得及修改，就阻塞了，这时线程B开始了，它也得到了i的值，由于i的值未被修改，即使是被volatile修饰，主存的变量还没变化，那么线程B得到的值也是100，之后对其进行加1操作，得到101后，将新值写入到缓存中，再刷入主存中。根据可见性的原则，这个主存的值可以被其他线程可见。
 问题来了，线程A已经读取到了i的值为100，也就是说读取的这个原子操作已经结束了，所以这个可见性来的有点晚，线程A阻塞结束后，继续将100这个值加1，得到101，再将值写到缓存，最后刷入主存，所以即便是volatile具有可见性，也不能保证对它修饰的变量具有原子性。）

