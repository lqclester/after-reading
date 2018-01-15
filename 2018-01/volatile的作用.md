> volatile关键字具有许多特性

## 可见性
其中最重要的特性就是保证了用volatile修饰的变量对所有线程的可见性。

当一个线程修改了变量的值，新的值会**立刻同步到主内存**当中。而其他线程读取这个变量的时候，也会从主内存中拉取最新的变量值。

## 防指令重排
> 指令重排是指JVM在编译Java代码的时候，或者CPU在执行JVM字节码的时候，对现有的指令顺序进行重新排序。

### 1. 防指令重排是通过内存屏障重现的

内存屏障共分为四种类型：

**LoadLoad屏障**：
抽象场景：Load1; LoadLoad; Load2
Load1 和 Load2 代表两条读取指令。在Load2要读取的数据被访问前，保证Load1要读取的数据被读取完毕。

**StoreStore屏障**：
抽象场景：Store1; StoreStore; Store2
Store1 和 Store2代表两条写入指令。在Store2写入执行前，保证Store1的写入操作对其它处理器可见

**LoadStore屏障**：
抽象场景：Load1; LoadStore; Store2
在Store2被写入前，保证Load1要读取的数据被读取完毕。

**StoreLoad屏障**：
抽象场景：Store1; StoreLoad; Load2
在Load2读取操作执行前，保证Store1的写入对所有处理器可见。StoreLoad屏障的开销是四种屏障中最大的。


## 什么时候适合用volatile呢？

1.运行结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。

2.变量不需要与其他的状态变量共同参与不变约束

## 其他作用
 volatile除了保证可见性和阻止指令重排，还解决了long类型和double类型数据的8字节赋值问题
 
# 记录
阅读[漫画：什么是volatile关键字？（整合版）](https://mp.weixin.qq.com/s/DZkGRTan2qSzJoDAx7QJag##)的总结
