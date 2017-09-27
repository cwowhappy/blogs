### Unsafe
> sun.misc.Unsafe

#### 1.应用场景
* 对变量和数组内容的原子访问，自定义内存屏障
* 对序列化的支持
* 对高级锁的支持
* 自定义内存管理/高效的内存布局
* 与原生代码和其它JVM进行互操作

#### 2.原子访问
java.util.concurrent.atomic.AtomicX
java.util.concurrent.atomic.AtomicXFieldUpdater

#### 3.序列化
Unsafe类的allocateInstance方法（`public native Object allocateInstance(Class<?> clazz)`）快速初始化对象，并避免调用构造器方法。

#### 4.对高级锁的支持

#### 5.内存管理

```java
ByteBuffer byteBuffer = ByteBuffer.allocateDirect(8);
byteBuffer.putLong(0, Long.MAX_VALUE);
byteBuffer.getLong(0);
```

#### 6.互操作
JNR-FFI
Panama项目和Valhalla项目

#### 参考博文
[1] http://www.infoq.com/cn/articles/A-Post-Apocalyptic-sun.misc.Unsafe-World