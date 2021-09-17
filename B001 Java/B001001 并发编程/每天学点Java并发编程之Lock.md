[TOC]

# API 说明

`lock()`：加锁

`unlock()`：解锁



# ReentrantLock

## 特性

-   完全的互斥排他性：同一时间只有一个线程执行。

## API

`newCondition()`：获取此锁的条件 Condition

`getHoldCount()`：查询当前线程保持锁的个数，即调用 locak() 方法的次数

`getQueueLength()`：返回正等待获取锁定的线程**估计数**，例如 10个线程，其中1个首先执行 await() 方法，那么还有4个线程同时在等待Lock的释放。

`getWaitQueueLength()`：返回等待与此锁相关的给点条件 Condition 的线程估计数，即 执行 awaite() 的线程数。

`hasQueuedThread(Thread thread)`：指定线程是否正在等待获取此锁

`hasQueuedThreads()`：是否有线程正在等待获取此锁

`hasWaiters()`：是否有线程正在等待，与此锁定有关的条件 condition.await()

`isFair()`：判断是否是公平锁

`isHeldByCurrentThread`：当前线程是否获取该锁

`isLocked()`：是否被任意线程获取锁

`lockInterruptibly()`：如果当前线程未被中断，则获取锁。如果已经被中断，则抛出异常。

`tryLock()`：**仅调用时**如果没有其他线程锁定，才能获取该锁。**非阻塞式**

`tryLock(long timeout, TimeUnit unit)`：给定时间段内如果没有其他线程锁定，且线程未被中断，则获取该锁。

`awaitUninterruptibly()`：等待期间忽略中断异常

`awaitUntil()`：等待指定时间后将自动唤醒，也可以被提前唤醒



## 常用结构

```java
Lock lock = new ReentrantLock();
try {
    lock.lock(); // if( lock.tryLock() ){...}else{...}
    ...
} finally {
    if (lock.isHeldByCurrentThread()) {
        lock.unlock();
    }
    
}
```

## 等待/通知机制

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
Condition conditionX = lock.newCondition(); // 通过配置多个条件对应不同的线程，从而实现唤醒指定线程
public void awite() {
    try {
        lock.lock(); // 必须在 condition.await(); 之前执行
        condition.await(); // 类似 wait()
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}

public void signal() {
    try {
        lock.lock(); // 必须在 condition.await() 之前执行
        condition.signal(); // 类似 notify()
        // condition.signalAll(); 类似 notifyAll()
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}

```

## 生产/消费模式

### 一对一场景

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
boolean flag = false; // 交替的条件

public void produce() {
    try {
        lock.lock();
       	while(!flag){
            condition.await();
        }
        ...
        flag = !flag;
        condition.sign();
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}

public void consume(){
    try {
        lock.lock();
       	while(flag){
            condition.await();
        }
        ...
        flag = !flag;
        condition.sign();
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}
```

### 多对多场景【《Java 多线程编程核心技术》4.1.7?】

交替条件可能存在假死情况，需要使用 signAll()

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
boolean flag = false; // 交替的条件

public void produce() {
    try {
        lock.lock();
       	while(!flag){
            condition.await();
        }
        ...
        flag = !flag;
        condition.sianAll();
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}

public void consume(){
    try {
        lock.lock();
       	while(flag){
            condition.await();
        }
        ...
        flag = !flag;
        condition.sianAll();
    } catch (InterruptedException e) {
		...
    } finally {
        lock.unlock();
    }
}
```

# ReentrantReadWriteLock

## 特性

-   读读共享
-   读/写互斥
-   写写互斥