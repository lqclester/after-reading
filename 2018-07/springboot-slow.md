# spirngboot在centos环境启动慢
> springboot-rlease-2.0.3 tomcat8，一开始我以为是熵池问题，因为我之前项目试过，但加了参数还是不行，启动项目要170s，

## 问题
卡在springboot标志之前，后来换了机器试过又可以很快启动7000ms

## 1. 熵问题
> 大部分网上都说于熵池不够大，之前我也遇到过

### 解决1:
```
安装熵服务
yum install rng-tools
启动熵服务
systemctl start rngd 
```
### 解决2:
```
java -Djava.security.egd=file:/dev/./urandom -jar xxxx.jar
```

## 2. host问题

https://www.2cto.com/net/201708/673190.html