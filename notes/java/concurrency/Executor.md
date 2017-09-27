### Java学习笔记之Executor
#### 1.相关接口和类
* Executor
* ExecutorService
* ScheduledExecutorService
* AbstractExecutorService
* ThreadPoolExecutor
* ForkJoinPool
* ScheduledThreadPoolExecutor
* Executors

这些类都在包java.util.concurrency下，它们的关系入下图所示：
![](/resources/class-diagram-java-executor.jpg)
#### 2.Executor接口
> since 1.5

```java
public interface Executor {
    void execute(Runnable command);
}
```
`Executor`接口的目的是解耦任务提交和任务调度执行机制。
注意点：
* 不严格要求异步执行任务
#### 3.ExecutorService接口
> since 1.5

```java
public interface ExecutorService extends Executor {
    void shutdown();
    List<Runnable> shutdownNow();
    boolean isShutdown();
    boolean isTerminated();
    boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;

    <T> Future<T> submit(Callable<T> task);
    <T> Future<T> submit(Runnable task, T result);
    Future<?> submit(Runnable task);

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;
    <T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException, ExecutionException;
}
```
#### 4.ScheduledExecutorService接口
> since 1.5

```java
public interface ScheduledExecutorService extends ExecutorService {
    ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit);
    <V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit);
    ScheduledFuture<?> scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit);
    ScheduledFuture<?> scheduleWithFixedDelay(Runnable command, long initialDelay, long period, TimeUnit unit);
}
```
#### 5.AbstractExecutorService抽象类
ExecutorService接口的默认实现
#### 6.ThreadPoolExecutor

#### 7.ForkJoinPool
#### 8.ScheduledThreadPoolExecutor
#### 9.Executors
