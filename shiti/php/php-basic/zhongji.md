## 简要描述PHP与NGINX的工作原理

[参考链接](http://www.qqdeveloper.com/2019/10/06/CGI-FastCGI-php-fpm/),[php-fpm配置说明](http://www.qqdeveloper.com/2020/05/21/php-fpm/),[参考地址](https://juejin.im/post/6861726452917174279)

## 简要概述PHP进程信号处理

[参考链接](https://juejin.im/post/6862458728675803150)

## PHP进程间通信的几种方式?

1. 消息队列。
2. 信号量+共享内存。
3. 信号。
4. 管道。
5. socket。

## 如何修改session的生存时间？

在php.ini 中设置： `session.gc_maxlifetime = 1440`

代码实现：
```php
$lifeTime = 24 * 3600; // 保存一天
session_set_cookie_params($lifeTime);
session_start();
```

## 一个php文件的解释过程是? 一般加速php有哪些? 提高php整体性能会用到哪些技术?

待完善中...

## session和cookie生存周期区别? 存储位置区别?

待完善中...
[参考链接](https://learnku.com/articles/33852)

##  php的内存回收机制是?

待完善中...

## 一个php文件的解释过程是? 一般加速php有哪些? 提高php整体性能会用到哪些技术?

待完善中...