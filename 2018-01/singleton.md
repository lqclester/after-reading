# 单例模式

## 实现1：锁+双重检测
>  线程安全 冷加载 不防反射
```
/**
 * 1. 私有构造方法
 * 2. 同步锁
 * 3. 双重检测方法
 **/
public class Singletonn {
    private Singletonn() {}

    private static Singletonn INSTANCE;

    public static Singletonn getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Singletonn();
                }
            }
        }
        return INSTANCE;
    }
}
```
## 实现2： 静态内部类
>  线程安全 冷加载 不防反射
```
public class Singleton {
    private static class LazyHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    private Singleton() {
    }

    public static Singleton getInstance() {
        return LazyHolder.INSTANCE;
    }

    public String getData() {
        return "data";
    }

}
```

## 实现3： 枚举
>  线程安全 非冷加载 防反射
```
public enum Singleton {
    INSTANCE;

    public String getData() {
        return "data";
    }

    public static void main(String[] args) {
        Singleton.INSTANCE.getData();
    }
}
```

# 后序

阅读[什么是单例模式](https://mp.weixin.qq.com/s?__biz=MzIxMjE5MTE1Nw==&mid=2653192251&idx=2&sn=4acce2985ab4fcc908235891c9213628&chksm=8c99f2e1bbee7bf7f64132bb58d3023f79b3c11fe2043dcd29fe07f4ddb5b3c7d375252d8555&scene=21#wechat_redirect)总结
