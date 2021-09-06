## ThreadLocal

#### 1.什么是ThreadLocal？用来解决什么问题的？

ThreadLoacl是一个将在多线程中为每一个单线程创建单独的变量副本的类，可以用来解决共享变量的线程安全问题。

#### 2.ThreadLocal是如何实现线程隔离的？

主要是用Thread类上的一个ThreadLocalMap类型的变量threadLocals，负责存储当前线程的关联对象。

存储：

- 如果当前线程有ThreadLocalMap类型的变量threadLocals，则直接存值
- 创建当前线程的ThreadLocalMap类型的变量threadLocals，key是当前ThreadLocal对象，value是set的value值

获取：

- 获取当前线程的threadLocals
- 如果threadLocals为空，则调用setInitialValue，初始化threadLocals并设置值
- 如果threadLocals不为空，直接返回值

#### 3.ThreadLocal内存泄漏问题？

ThreadLocalMap中的key是弱引用，value是强引用。如果ThreadLocal没有被外部强引用的情况下，key会被清理掉，而value不会被gc，这样就会出现key为null的Entry了。

ThreadLocal考虑了这种情况，使⽤完 ThreadLocal ⽅法后 最好⼿动调⽤ remove() ⽅法。

