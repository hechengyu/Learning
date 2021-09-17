[TOC]



# 1.Feture

`boolean cancal(boolean)`

-   任务还没开始
-   返回false
-   任务已经启动

    -   cancel(true)，中断正在运行的任务，中断成功，返回true

    -   cancel(false)，不会去中断已经运行的任务
-   任务已经结束
    -   返回false

`V get()`

`V get(long timeout, TimeUnit unit)`

`boolean isDone()`：结束，正常还是异常结束，或者自己取消，返回true；

`boolean isCancelled()`：任务完成前被取消，返回true；



## 1.1 FutureTask

```java
FutureTask<Integer> futureTask = new FutureTask<>(() -> {
    Thread.sleep(2000);
    return 0;
});
new Thread(futureTask).start();
futureTask.get();
// futureTask.get(500);
// futureTask.cancel(true);
```



## 1.2 ForkJoinTask

## 1.3 RecursiveAction

# 2 Fork/Join

**什么是分而治之？**

规模为N的问题，N<阈值，直接解决，N>阈值，将N分解为K个小规模子问题，子问题互相对立，与原问题形式相同，将子问题的解合并得到原问题的解。动态规范

步骤：

-   分割任务

-   执行任务并合并结果

    



## 2.1 工作密取(work-stealing)算法

`work-stealing`：指某个线程从其他队列窃取任务来执行。

通常采用**双端队列**，被窃取任务的线程永远从双端队列的头部获取任务执行，窃取任务的线程冲双端队列的尾部获取任务执行。

优点：充分利用线程进行并行计算，减少线程间的竞争

缺点：某种情况下依旧存在竞争，比如双端队列中只有一个任务。

## 2.2 Fork/Join框架

### 2.2.1 ForkJoinTask

-   RecursiveAction：没有返回结果的任务
-   RecursiveTask：有返回结果的任务

### 2.2.2 ForkJoinPool



### 2.2.3 异常处理

`isCompletedAbnormally()`：检查任务是否已经抛出异常或已经被取消了。

`getException()`：返回 Throwable 对象

-   CancellationException：任务被取消
-   null：任务没有完成或者没有抛出异常

```java
if(task.isCompletedAbnormally()){
    // task.getException();
}
```



## 2.3 实现原理

### 2.3.1 状态

-   已完成：返回结果

-   被取消：抛出 CancellationException
-   信号
-   出现异常：抛出对应的异常

## 2.4 标准范式

```java
public class MyTask extends RecursiveAction<T> {
	@Override
    protected T compute() {
		//...
       	MyTask task = new MyTask()
        invokeAll(task);
        task.join();        
    }
}

ForkJoinPool pool = new ForkJoinPool();
MyTask task = new MyTask();
pool.invoke(task);
task.join();
```



## 2.5 实战案例

### 2.5.1 统计数组总和

```java
private static class SumTask extends RecursiveTask<Integer>{
    private final static int THRESHOLD = MakeArray.ARRAY_LENGTH/10;
    private int[] src;
    private int fromIndex;
    private int toIndex;

    public SumTask(int[] src, int fromIndex, int toIndex) {
        this.src = src;
        this.fromIndex = fromIndex;
        this.toIndex = toIndex;
    }

    @Override
    protected Integer compute() {
        if(toIndex-fromIndex < THRESHOLD) {
            int count = 0;
            for(int i=fromIndex;i<=toIndex;i++) {
                count = count + src[i];
            }
            return count;
        }else {
            int mid = (fromIndex+toIndex)/2;
            SumTask left = new SumTask(src,fromIndex,mid);
            SumTask right = new SumTask(src,mid+1,toIndex);
            invokeAll(left,right);
            return left.join()+right.join();
        }
    }
}

ForkJoinPool pool = new ForkJoinPool();
int[] src = new int[1000];
// ...
SumTask innerFind = new SumTask(src,0,src.length-1);
pool.invoke(innerFind);
innerFind.join();
```



### 2.5.2 遍历目录和子目录

```java
public class FindDirsFiles extends RecursiveAction {
	@Override
	protected void compute() {
		List<FindDirsFiles> subTasks = new ArrayList<>();
		File[] files = path.listFiles();
		if(files!=null) {
			for(File file:files) {
				if(file.isDirectory()) {
					subTasks.add(new FindDirsFiles(file));
				}else {
                    System.out.println("文件: " + file.getAbsolutePath());
				}
			}
			if(!subTasks.isEmpty()) {
				for(FindDirsFiles subTask : invokeAll(subTasks)) {
					subTask.join();//等待子任务执行完成
				}
			}
		}
	}
}
```

# 3.工具类

## 3.1 CountDownLatch

作用：是一组线程等待其他的线程完成工作以后在执行，加强版join

await用来等待，countDown负责计数器的减一

```java
CountDownLatch latch = new CountDownLatch(2);
static class MyThread implements Runnable {
    @Override
    public void run() {
        try {
            Thread.sleep(2*1000);
        } catch (InterruptException e) {
            //...
        }
        latch.countDown();
    }
}
for(int i = 0; i < 2; i++){
    new MyThread().start();
}
latch.await();
```



## 3.2 CyclicBarrier

让一组线程达到某个屏障，被阻塞，一直到组内最后一个线程达到屏障时，屏障开放，所有被阻塞的线程会继续运行CyclicBarrier(int parties)

CyclicBarrier(int parties, Runnable barrierAction)，屏障开放，barrierAction定义的任务会执行

CountDownLatch和CyclicBarrier辨析

1、countdownlatch放行由第三者控制，CyclicBarrier放行由一组线程本身控制
 2、countdownlatch放行条件》=线程数，CyclicBarrier放行条件=线程数

```java
CyclicBarrier barrier = new CyclicBarrier(5, () -> {
    @Override
    public void run() {
        // ..
    }
});
for(int i = 0; i < 2; i++){
    new Thread(() -> {
      @Override
      public void run() {
          // ..
          barrier.await();
      }  
    }).start();
}

```



## 3.3 Semaphore

控制同时访问某个特定资源的线程数量，用在流量控制



## 3.4 Exchanger

**两个线程间**的数据交换。

如果没有线程未交换数据，则引起阻塞。

```java
private static final Exchanger<Set<String>> exchange = new Exchanger<Set<String>>();
new Thread(() -> {
    @Override
    public void run() {
        Set<String> set = new HashSet<String>();
        try {
            set.add("Thread-A");
            set = exchange.exchange(set);//交换set
            /*处理交换后的数据*/
            System.out.println(Thread.currentThread().getName() + set);
        } catch (InterruptedException e) {
        }
    }
}, "Thread-A").start();
new Thread(() -> {
    @Override
    public void run() {
        Set<String> set = new HashSet<String>();
        try {
            set.add("Thread-B");
            set = exchange.exchange(set);//交换set
            /*处理交换后的数据*/
            System.out.println(Thread.currentThread().getName() + set);
        } catch (InterruptedException e) {
        }
    }
}, "Thread-B").start();
```

