# 重温单例模式

## 单例模式
实现：
1. 懒汉模式： 后初始化
2. 饿汉模式： 先初始化 instance = new Singleton();
3. 静态内部类
`以上都不防反射`

## 单例模式（懒汉 双重锁版）
### 写的思路
1. 私有构造方法
2. 单例对象
3. 静态工厂方法
`到此非线程安全`
4. 双重锁 -> synchronized + 判断
5.  volidate 内存屏障
```
新建对象 new Object()
情况1：
    1：分配对象的内存空间
    2：初始化对象  -> instance != null
    3：设置instance指向刚分配的内存地址
结果：另外一条线程获取了对象，但空数据
情况2：
    1：分配对象的内存空间
    3：设置instance指向刚分配的内存地址  instance = null
    2：初始化对象  -> instance != null
结果：另外一条线程覆盖了原来的线程数据
```
### 代码
```

public class Singleton {
    private Singleton() {
        // 私有构造方法
    }
    private volatile static Singleton instance = null;

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance ==null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

## 静态内部类
### 编写思路

1. 静态内部类
2. 静态构造方法
3. 静态工厂方法

### 代码
```
public class InnerSingleton {

    private static class LazyHolder {
        private static final InnerSingleton INSTANCE = new InnerSingleton();
    }

    private InnerSingleton() {}

    public static InnerSingleton getInstance() {
        return LazyHolder.INSTANCE;
    }
}
```

## 枚举模式
### 代码
```
public enum  SingletonEnum {
    INSTANCE;
}
```

## 参考
[漫画：什么是单例模式？（整合版](https://mp.weixin.qq.com/s?__biz=MzIxMjE5MTE1Nw==&mid=2653192251&idx=2&sn=4acce2985ab4fcc908235891c9213628&chksm=8c99f2e1bbee7bf7f64132bb58d3023f79b3c11fe2043dcd29fe07f4ddb5b3c7d375252d8555&scene=21#wechat_redirect)
