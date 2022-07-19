## 说明

本文演示如何在Linux环境搭建elasticsearch。使用的是centos7.9。

## 安装步骤

### 安装Java环境

安装Java环境，可以多种方式，文章演示直接使用`yum`安装方式

```shell
yun install -y java
```
检测安装结果，如出现下面的情况则表示安装成功。
```shell
$ java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```

### 创建es账号

由于elasticsearch默认情况下是不允许使用root账号启动，在启动的时候也提示并失败。因此需要创建一个独立的账号，来启动elasticsearch服务器。
```shell
# 创建es账号
useadd esuser
# 设置密码
passwd esuser
......
#根据提示输入密码信息
......
```

### 下载es源码

源码[下载地址](https://www.elastic.co/cn/downloads/past-releases/elasticsearch-7-10-2)。
```shell
cd /opt && wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-linux-x86_64.tar.gz.sha512

tar -zxvf elasticsearch-7.10.2-linux-x86_64.tar.gz && mv elasticsearch-7.10.2-linux-x86_64.tar.gz es
```

### 切换到es账号

```shell
sudo esuser
```

### 启动es服务

```shell
cd es/bin && elasticsearch
```

### 测试服务

新建一个连接窗口，执行下面的命令。出现下面的结果，则表示成功启动。
```shell
[root@ecs-64550521 es]# curl http://127.0.0.1:9200
{
  "name" : "ecs-64550521",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "hNMbHfr8Rw2JHQWe5twxug",
  "version" : {
    "number" : "7.10.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "51e9d6f22758d0374a0f3f5c6e8f3a7997850f96",
    "build_date" : "2020-11-09T21:30:33.964949Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"     
  },
  "tagline" : "You Know, for Search"
}
```
