>   synchronized基于阻塞的锁机制缺点
>
>   1、被阻塞的线程优先级很高
>
>   2、拿到锁的线程一直不释放锁怎么办
>
>   3、大量的竞争，消耗cpu，同时带来死锁或者其他安全



# 1.原子操作CAS

>   CAS: Compare And Swap

`指令级别`保证是原子操作

三个运算符： 一个内存地址V，一个期望的值A，一个新值B

基本思路：如果地址V上的值和期望的值A相等，就给地址V赋给新值B，如果不是，不做任何操作。

循环（死循环，`自旋`）里不断的进行CAS操作，



存在的问题：

-   ABA问题：解决方案：版本号。A1>B2>A3
-   开销：CAS操作长期不成功，cpu不断的循环
-   只能保证一个共享变量的原子操作：将多个变量封装成一个对象



>   AtomicReference更新的不是引用的对象



ABA的典型处理方案

**AtomicMarkableReference**：

**AtomicStampedReference**：