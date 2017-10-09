## Java同步之`synchronized`
### 1.Monitor Object模式
#### 1.1.四种类型的参与者
* 监视者对象：负责定义公共的接口方法，这些公共的接口方法会在多线程的环境下被调用
* 同步方法：这些方法是由监视者对象所定义。为了防止竞争条件，无论是否同时多个线程并发调用同步方法，还是监视者对象含有多个同步方法，在任一时间内只有监视者对象的一个同步方法能够被执行
* 监视锁：每个监视者对象都会拥有一把监视锁
* 监视条件：同步方法使用监视锁和监视条件来决定是否需要阻塞或者重新执行
#### 1.2.参与者之间的协作过程
1. 同步方法的调用和串行化：当客户线程调用监视者对象的同步方法时，必须首先获取它的监视锁；只要该监视者对象有其它同步方法正在被执行，锁获取操作便不会成功。在这种情况下，客户线程将被阻塞直到它获取到监视锁；当客户线程成功获取监视锁后，线程进入临界区并执行。一旦同步方法完成执行，监视锁会被自动释放。
2. 同步方法线程挂起：如果调用同步方法的客户线程必须被阻塞或有其它原因不能立刻执行，它能够在一个监视条件上等待，这将导致该客户线程暂时释放监视锁，并被挂起在监视条件上。
3. 监视条件通知：一个客户线程能够通知一个监视条件，目的是为了让一个前期使自己挂起在一个监视条件上的同步方法线程恢复运行。
4. 同步方法线程恢复：一旦一个早先被挂起在监视条件上的同步方法线程获取通知，它将继续在最初的等待监视条件的地方执行（在被通知线程被允许恢复执行同步方法之前，监视锁将被自动获得）。

Monitor Object时序图：
![](/resources/monitor-object-sequence-diagram.jpg)
### 2.`synchronized`的实现
### 3.`Object`的`wait`方法和`notify`方法
### 4.例子：阻塞队列
```java
public class SimpleBlockingQueue<E> {
    private static final int QUEUE_LENGTH = 16;
    private Object[] elements;
    private int count;
    private int indexHead;
    private int indexTail;

    private final Object guard = new Object();
    private final Object notFull = new Object();
    private final Object notEmpty = new Object();

    public SimpleBlockingQueue() {
        elements = new Object[QUEUE_LENGTH];
        count = indexHead = indexTail = 0;
    }

    public E take() throws InterruptedException {
        E value;
        synchronized (notEmpty) {
            while (0 == count) {
                notEmpty.wait();
            }
            synchronized (guard) {
                value = (E) elements[indexHead];
                count --;
                indexHead ++;
                if(indexHead == QUEUE_LENGTH) {
                    indexHead = 0;
                }
            }

        }
        synchronized (notFull) {
            notFull.notify();
        }
        return value;
    }

    public void offer(E element) throws InterruptedException {
        synchronized (notFull) {
            while (count == QUEUE_LENGTH) {
                notFull.wait();
            }
            synchronized (guard) {
                elements[indexTail] = element;
                count ++;
                indexTail ++;
                if (QUEUE_LENGTH == indexTail) {
                    indexTail = 0;
                }
            }
        }
        synchronized (notEmpty) {
            notEmpty.notify();
        }
    }
}
```
### 5.参考文献
[1]. https://www.ibm.com/developerworks/cn/java/j-lo-synchronized/
[2]. http://blog.csdn.net/wwh578867817/article/details/52004265
[3]. 