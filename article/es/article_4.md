## 说明

本文将演示如何在Mac系统中，安装elasticsearch。因为elasticsearch是Java开发，因此需要配置Java开发环境。本文省略此步骤。

## 提前准备

由于brew默认使用的源非常慢，推荐使用该仓库的配置。[Gitee地址](https://gitee.com/cunkai/HomebrewCN)。

## 开始安装

假设你已经做好了所有的准备工作。本文就开始从安装环境开始讲起了。

### brew版本检测

我们检测brew源的elasticsearch版本号，安装的其他服务就以该服务为基准，选择对应的版本号。通过使用`brew info elasticsearch`命令，我们可以查看到elasticsearch的版本号，以及对应的依赖(下面✅的部分就是对应的依赖包)，如果你查询到是❌ ，你使用`brew install gradle@6`命令安装即可，其他的依赖包同样按照此方式安装即可。 
```shell
 olddog@192  ~brew info elasticsearch
elasticsearch: stable 7.10.2 (bottled)
Distributed search & analytics engine
https://www.elastic.co/products/elasticsearch
Deprecated because it is switching to an incompatible license. Check out `opensearch` instead!
/usr/local/Cellar/elasticsearch/7.10.2 (156 files, 113.5MB) *
  Poured from bottle on 2022-02-20 at 15:46:47
From: https://mirrors.ustc.edu.cn/homebrew-core.git/Formula/elasticsearch.rb
License: Apache-2.0
==> Dependencies
Build: gradle@6 ✔
Required: openjdk ✔
==> Caveats
Data:    /usr/local/var/lib/elasticsearch/
Logs:    /usr/local/var/log/elasticsearch/elasticsearch_kert.log
Plugins: /usr/local/var/elasticsearch/plugins/
Config:  /usr/local/etc/elasticsearch/

To have launchd start elasticsearch now and restart at login:
  brew services start elasticsearch
Or, if you don't want/need a background service you can just run:
  elasticsearch
==> Analytics
install: 2,484 (30 days), 6,719 (90 days), 34,351 (365 days)
install-on-request: 2,477 (30 days), 6,702 (90 days), 34,273 (365 days)
build-error: 344 (30 days)
```

### 安装elasticsearch

确认好安装的版本是`7.10.2`，那我们就可以开始安装。
```shell
// 安装elasticsearch
brew install elasticsearch

// 启动服务
brew services start elasticsearch
```
安装好之后，相关的数据目录以及配置目录都会显示出来，大致如下：
```shell
Data:    /usr/local/var/lib/elasticsearch/
Logs:    /usr/local/var/log/elasticsearch/elasticsearch_kert.log
Plugins: /usr/local/var/elasticsearch/plugins/
Config:  /usr/local/etc/elasticsearch/
```
安装好之后，我们本地服务就启动正常了。直接访问`http://127.0.0.1:9200/`，出现下面的信息，表示我们的elasticsearch，完全安装成功。

![Snipaste_2022-02-23_22-29-12](http://qiniucloud.qqdeveloper.com/1645626629889-Snipaste_2022-02-23_22-29-12.png)

同时，我们也可以使用一个开源软件查看。这里就不讲怎么使用了，[软件地址](https://github.com/qax-os/ElasticHD)。在安装好之后，我们先执行一下这个命令，向elasticsearch中些一条数据。
```shell
url -H "Content-Type: application/json" -XPOST 'http://127.0.0.1:9200/system-syslog-20181129/system-syslog' -d '{"first_name":"yuan","last_name":"mu","age":88,"about":"I love to wo qu","interests":["sport","huangya"]}'
》 {"_index":"system-syslog-20181129","_type":"system-syslog","_id":"sF0mHX8BBJXzIcLuz4gM","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```