JAVA设计模式-单例
===


## 双重检查模式

### 使用场景 -单例模型 

>* _单例有以下实现方式：_

```java

public class Singleton {  

 private static Singleton instance;  

 private Singleton() {  

 }  

 public static Singleton getInstance() {  

 if (instance == null) {  

 instance = new Singleton();  

 }  

 return instance;  

 }  

```

>* _以上方式称为延迟初始化，但是在多线程的情况下会失效，于是使用同步锁，给getInstance() 方法加锁：_

```java

 public static synchronized Singleton getInstance() {  

 if (instance == null) {  

 instance = new Singleton();  

 }  

 return instance;  

 }  

``` 

### 引出双重检查模式                                     

这段代码的目的是对 resource 延迟初始化。但是每次访问的时候都需要同步，导致执行性能下降。为了减少同步的开销，于是有了双重检查模式

```java

public static Singleton getInstance() {  

 if (instance == null) {  

 synchronized (Singleton.class) {  

 if (instance == null) { A线程    

 instance = new Singleton();  B线程 

 }  

 }  

 }  

 return instance;  

 }  

```

### 数据重排序

指令重排序是为了优化指令，提高程序运行效率。指令重排序包括编译器重排序和运行时重排序。JVM规范规定，指令重排序可以在不影响单线程程序执行结果前提下进行。例如 instance = new Singleton() 可分解为如下伪代码：

```java

memory = allocate();  //1：分配对象的内存空间  

ctorInstance(memory); //2：初始化对象  

instance = memory;  //3：设置instance指向刚分配的内存地址 

```

但是经过重排序后如下：

```java

memory = allocate();  //1：分配对象的内存空间  

instance = memory;  //3：设置instance指向刚分配的内存地址  

 //注意，此时对象还没有被初始化！  

ctorInstance(memory); //2：初始化对象 

```

问题点：

将第2步和第3步调换顺序，在单线程情况下不会影响程序执行的结果，但是在多线程情况下就不一样了。线程A执行了instance = memory（这对另一个线程B来说是可见的），此时线程B执行外层 if (instance == null)，发现instance不为空，随即返回，但是得到的却是未被完全初始化的实例，在使用的时候必定会有风险，这正是双重检查锁定的问题所在！

### 解决方案

>* _可以使用volatile变量禁止指令重排序_

```java

public class Singleton {  

 private static volatile Singleton instance;  

 private Singleton() {  

 }  

 public static Singleton getInstance() {  

 if (instance == null) {  

 synchronized (Singleton.class) {  

 if (instance == null) {  

 instance = new Singleton();  

 }  

 }  

 }  

 return instance;  

 }  

} 

```

>* _延迟初始化占位（Holder）类模式:在内部类中提前初始化实例，既保证了Singleton实例的延迟初始化，又保证了同步。这是一种提前初始化（恶汉式）和延迟初始化（懒汉式）的综合模式_

```java

public class Singleton {  

 private static Singleton instance = new Singleton();  

 private Singleton() {  

 }  

 public static Singleton getInstance() {  

 return instance;  

 }  

} 

```

## volatitle