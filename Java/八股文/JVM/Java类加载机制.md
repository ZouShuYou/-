## Java类加载机制

#### 1.类的生命周期

![类的生命周期](..\img\java_jvm_classload_2.png)

- 加载
- 连接：包括验证、准备、解析
- 初始化
- 使用
- 卸载

加载时类加载过程的第一个阶段，在加载阶段，虚拟机需要完成以下三件事情:

- 通过一个类的全限定名来获取其定义的二进制字节流。
- 将字节流所代表的静态存储结构转换为方法区的运行时数据结构。
- 在内存中生成一个代表该类的Class对象，作为方法区这些数据的访问入口。



#### 2.类加载器

![java_jvm_classload](..\img\java_jvm_classload_3.png)

**站在Java开发人员的角度来看，类加载器可以大致划分为以下三类** :

- 启动类加载器（BootStrap ClassLoader）：最顶层的类加载器，由C++实现，负责加载`%JAVA_HOME%\jre\lib`目录下的jar包和类或者被`-Xbootclasspath`参数指定的路径中的所有类
- 扩展类加载器（Extension ClassLoader）：它负责加载`JDK\jre\lib\ext`目录下的jar包或类，或者由`java.ext.dirs`系统变量指定的路径中的所有类库(如javax.*开头的类)，开发者可以直接使用扩展类加载器。
- 应用类加载器（Application ClassLoader）：它负责加载用户类路径(ClassPath)所指定的类，开发者可以直接使用该类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

#### 3.JVM类加载机制

- 全盘负责：当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入
- 父类委托：先让父类加载器试图加载该类，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类
- 缓存机制：缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区寻找该Class，只有缓存区不存在，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区。这就是为什么修改了Class后，必须重启JVM，程序的修改才会生效
- 双亲委派机制：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。

#### 4.双亲委派机制过程

- 当AppClassLoader加载一个class时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器ExtClassLoader去完成。
- 当ExtClassLoader加载一个class时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给BootStrapClassLoader去完成。
- 如果BootStrapClassLoader加载失败(例如在`%JAVA_HOME%\jre\lib`里未查找到该class)，会使用ExtClassLoader来尝试加载
- 若ExtClassLoader也加载失败，则会使用AppClassLoader来加载，如果AppClassLoader也加载失败，则会报出异常ClassNotFoundException。

