# 读：什么是Java内存模型
> time:2018/03/14

1. Java内存模型是JVM的抽象模型（主内存，本地内存）

在Java内存模型中，描述了在多线程代码中，哪些行为是正确的、合法的，以及多线程之间如何进行通信，代码中变量的读写行为如何反应到内存、CPU缓存的底层细节。

## 区分内存结构
*不同于内存结构*，内存结构指jvm内存的管理与分配的机制


# synchronized
1. 互斥功能
2. 保证了线程在同步块之前或者期间写入动作，对于后续进入该代码块的线程是可见的
3. 使CPU缓存失效，从而使变量从主内存中重新加载

# volatile可以做什么

```
public class VolatileExample {

    int x =0;
    volatile boolean v = false;

    public void write() {
        x =42;
        v = true;
    }

    public void read() {
        if (v) {
            // thread.A 调用write, thread.B 调用read, v= true,x一定等于42
        }
    }
}
```
# 读文
[什么是Java内存模型](https://mp.weixin.qq.com/s/F425mhUVvKDoDeAWujJRTw)
