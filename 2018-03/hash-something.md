# hashMap常见问题
## 1. 长度为什么要是 length = 2^n
### 1.1 index =  HashCode（Key） &  （Length - 1）
Hash算法最终得到的index结果，完全取决于Key的Hashcode值的最后几位。

### 1.2 length=2^n-1
1. 当length=2^n-1 二进制 全是1111111111，则HashCode是随机 index则随机
2. 当length!=2^n-1 二进制 全是10101，则某些index特别多，某些少

## 2. 什么时候rehash
### 2.1 名词解释
1. Capacity HashMap的当前长度。
2. LoadFactor HashMap负载因子，默认值为0.75f。

### 2.2 resize,then rehash
当HashMap.Size>=Capacity * LoadFactor


## 线程不安全
发生时间: resize

线程B:
e=Entry3
next=Entry2
挂起（线程A finish）
e的index=3
线程A：
```
null null  null   Entry2   Entry1   Entry4 null
                    |
                 Entry3
```

# ConcurrentHashMap
jdk7 segment

当执行put方法插入数据时，根据key的hash值，在Segment数组中找到相应的位置，如果相应位置的Segment还未初始化，则通过CAS进行赋值，接着执行Segment对象的put方法通过加锁机制插入数据

实现如下：

场景：线程A和线程B同时执行相同Segment对象的put方法

1、线程A执行tryLock()方法成功获取锁，则把HashEntry对象插入到相应的位置；
2、线程B获取锁失败，则执行scanAndLockForPut()方法，在scanAndLockForPut方法中，会通过重复执行tryLock()方法尝试获取锁，在多处理器环境下，重复次数为64，单处理器重复次数为1，当执行tryLock()方法的次数超过上限时，则执行lock()方法挂起线程B；
3、当线程A执行完插入操作时，会通过unlock()方法释放锁，接着唤醒线程B继续执行；


ReentrantLock
CAS进行赋值

jdk8 链表变为红黑树
