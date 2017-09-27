### 1.相关类及其类关系图
* [I]`BlockingQueue`
* [C]`ArrayBlockingQueue`
* [C]`LinkedBlockingQueue`
* [C]`PriorityBlockingQueue`
* [C]`DelayQueue`
* [C]`SynchronousQueue`
![](/resources/class-diagram-java-blockingqueue.jpg)
### 2.[I]BlockingQueue
> since 1.5

`BlockingQueue`继承自`Queue`，定义了入队和出队的阻塞操作。
```java
public interface BlockingQueue<E> extends Queue<E> {
    boolean add(E e);
    boolean offer(E e);
    boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;
    void put(E e) throws InterruptedException;

    E task() throws InterruptedException;
    E poll(long timeout, TimeUnit unit) throws InterruptedException;

    int remainCapacity();
    boolean remove(Object o);
    boolean contains(Object o);
    int drainTo(Collection<? super E> c);
    int drainTo(Collection<? super E> c, int maxElements);
}
```
### 3.[C]ArrayBlockingQueue
> since 1.5

`ArrayBlockingQueue`继承了抽象类`AbstractQueue`，实现了接口`BlockingQueue`和`Serializable`.

`ArrayBlockingQueue`使用数组存储队列中的元素，类定义中几个重要的字段
```java
final Object[] items;
int taskIndex;
int putIndex;

final ReentrantLock lock;
private final Condition notEmpty;
private final Condition notFull;
```
#### 3.1.方法`add`
`public boolean add(E e)`：
* 逻辑：（线程安全）在队尾插入新元素，如果插入成功则返回`true`，如果队列满则抛出异常`IllegalStateException`，如果元素为`null`则抛出异常`NPE`
* 参数：待插入队列的元素
* 具体实现：调用父类的方法`add(E e)`，父类调用方法`offer(E e)`，具体逻辑依赖于方法`offer`在本类中的具体实现。
#### 3.2.方法`offer`
`public boolean offer(E e)`
* 逻辑：（线程安全）在队尾插入新元素，如果插入成功则返回`true`，如果队列满则返回`false`，如果元素为`null`则抛出异常`NPE`
* 参数：待插入队列的元素
* 具体实现：
```java
public boolean offer(E e) {
    checkNotNull(e);
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        if (count == items.length) {
            return false;
        } else {
            enqueue(e);
            return true;
        }
    } finally {
        lock.unlock();
    }
}
```
`public boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException`
* 逻辑：（线程安全）在队尾插入新元素，如果插入成功则返回true，如果队列满则等待设定的时间，超时则返回false，如果元素为`null`则抛出异常`NPE`，如果线程被中断则抛出异常`InterruptedException`
* 参数：新增元素和等待时长
* 具体实现：
```java
public boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException {
    checkNotNull(e);
    long nanos = unit.toNanos(timeout);
    final ReetrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == items.length) {
            if (0 >= nanos) {
                return false;
            }
            nanos = notFull.awaitNanos(nanos);
        }
        enqueue(e);
        return true;
    } finally {
        lock.unlock();
    }
}
```
#### 3.3.方法`put`
`public void put(E e) throws InterruptedException`
* 逻辑：（线程安全）在队尾插入新元素，如果队列满则阻塞线程，直到队列有位置插入新的元素或者被该线程被中断，如果新增元素为`null`则抛出异常`NPE`，如果线程被中断则抛出异常`InterruptedException`
* 参数：新增元素
* 具体实现：
```java
public void put(E e) throws InterruptedException {
    checkNotNull(e);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == items.length) {
            notFull.await();
        }
        enqueue();
    } finally {
        lock.unlock();
    }
}
```
#### 3.4.方法`poll`
`public E poll()`
* 逻辑：（线程安全）如果队列为空则返回`null`，否则就返回队首元素
* 参数：无
* 具体实现：
```java
public E poll() {
    final ReetrantLock lock = this.lock;
    lock.lock();
    try {
        return (0 == count) ? null : dequeue();
    } finally {
        lock.unlock();
    }
}
```
`public E poll(long timeout, TimeUnit unit) throws InterruptedException`
* 逻辑：（线程安全）如果队列为空则等待设置的时长，超时则返回`null`，否则返回队首元素，如果线程被中断则抛出异常`InterruptedException`
* 参数：无
* 具体实现：
```java
public E poll(long timeout, TimeUnit unit) throws InterruptedException {
    long nanos = unit.toNanos(timeout);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (0 == count) {
            if (0 >= nanos) {
                return null;
            }
            nanos = notEmpty.awaitNanos(nanos);
        }
        return dequeue();
    } finally {
        lock.unlock();
    }
}
```
#### 3.5.方法`take`
`public E take() throws InterruptedException`
* 逻辑：（线程安全）如果队列为空则阻塞该线程，直到队列中有元素并返回队首元素，或者线程被中断
* 参数：无
* 具体实现：
```java
public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (0 == count) {
            notEmpty.await();
        }
        return dequeue();
    } finally {
        lock.unlock();
    }
}
```
#### 3.6.辅助方法`enqueue`和`dequeue`

### 4.LinkedBlockingQueue
### 5.PriorityBlockingQueue