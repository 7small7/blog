## 说明

本文将演示如何在Mac系统中，安装ELK环境(elasticsearch、logstash、kibana)。在Mac上安装ELK非常简单，直接使用brew命令安装即可。同时网络上存在非常多的文章。但是99%的文章，都没有提出其中遇到的问题或者没提及到需要注意的事项。本文将重点介绍这些细节。

同时希望你在阅读本文的时候，耐心阅读。即使不能帮助你遇到的问题，但是可以大致给你一个解决思路。

## ELK逻辑图
![Snipaste_2022-02-23_22-57-54](http://qiniucloud.qqdeveloper.com/1645628283369-Snipaste_2022-02-23_22-57-54.png)

## 提前准备

由于brew默认使用的源非常慢，推荐使用该仓库的配置。[Gitee地址](https://gitee.com/cunkai/HomebrewCN)。

## 开始安装

假设你已经做好了所有的准备工作。本文就开始从安装环境开始讲起了。

### 版本检测

在使用ELK时，一定要注意版本的一致性，否则安装好之后会出现服务之间版本不兼容问题。如果你不是很清楚的情况，你可以使用下面的链接，来确保版本是否一致。强烈推荐版本号保持一致。例如你的elasticsearch安装的版本是`7.10.2`，推荐你在安装logstash时也选择一样的版本号`7.10.2`。[官网版本号总结](https://www.elastic.co/cn/support/matrix#matrix_compatibility)。在本文的演示中，我安装的版本是`7.10.2`。
![Snipaste_2022-02-23_22-20-31](http://qiniucloud.qqdeveloper.com/1645626049364-Snipaste_2022-02-23_22-20-31.png)

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

![Snipaste_2022-02-23_23-21-32](http://qiniucloud.qqdeveloper.com/1645629744832-Snipaste_2022-02-23_23-21-32.png)


### kibanna版本检测

下面的信息和elasticsearch其实都一样的，对 ✅ 和 ❌ 的对应的依赖包，如果是提示❌ 使用brew安装就可以了。下面的命令，我们可以看到版本号和elasticsearch的版本号是一致的，都是`7.10.2`，因此我们直接安装就可以了。
```shell
olddog@192  brew info kibana
kibana: stable 7.10.2 (bottled), HEAD
Analytics and search dashboard for Elasticsearch
https://www.elastic.co/products/kibana
Deprecated because it is switching to an incompatible license!
/usr/local/Cellar/kibana/7.10.2 (29,154 files, 300.8MB) *
  Poured from bottle on 2022-02-20 at 18:22:35
From: https://mirrors.ustc.edu.cn/homebrew-core.git/Formula/kibana.rb
License: Apache-2.0
==> Dependencies
Build: python@3.9 ✔, yarn ✔
Required: node@10 ✔
==> Options
--HEAD
```

### 安装kibanna

```shell
// 安装服务
brew install kibana

// 启动服务
brew services start kibana
```
安装好之后，相关的数据目录以及配置目录都会显示出来，对应的目录都在`/usr/local/Cellar/kibana/7.10.2`下面。可以通过`http://127.0.0.1:5601/app/home`，看到下面的预览图，说明我们已经安装成功。
![Snipaste_2022-02-23_22-42-54](http://qiniucloud.qqdeveloper.com/1645627384160-Snipaste_2022-02-23_22-42-54.png)


### logstash版本检测

同样是用上面提到的命令检测一下服务版本。
```shell
olddog@192   brew info logstash
logstash: stable 7.13.1 (bottled), HEAD
Tool for managing events and logs
https://www.elastic.co/products/logstash
Not installed
From: https://mirrors.ustc.edu.cn/homebrew-core.git/Formula/logstash.rb
License: Apache-2.0
==> Dependencies
Required: openjdk@11 ✔
==> Options
```
我们可以看到对应的版本是`7.13.1`，并且官方文档也提到elasticsearch的版本`7.10.2`是支持logstash对应的版本号`6.8.x-7.17.x`。

实际情况在使用brew安装后，是没法使用的。一直提示版本不兼容，无法建立连接。大致如下的错误信息：
```shell
Elasticsearch setup did not complete normally, please review previously logged errors
Unable to connect to Elasticsearch at http://localhost:9200
```
因此该服务，我们就推荐使用源码安装。其实，上面的两个服务也可以通过源码安装，只不过麻烦一点。

### 下载源码

默认[官网](https://www.elastic.co/cn/downloads/logstash)打开，是显示最新的版本，你可以自己选择对应的版本号。注意下图画框的部分。
![Snipaste_2022-02-23_22-46-20](http://qiniucloud.qqdeveloper.com/1645627607555-Snipaste_2022-02-23_22-46-20.png)

![Snipaste_2022-02-23_22-58-32](http://qiniucloud.qqdeveloper.com/1645628325262-Snipaste_2022-02-23_22-58-32.png)

![Snipaste_2022-02-23_22-59-24](http://qiniucloud.qqdeveloper.com/1645628375461-Snipaste_2022-02-23_22-59-24.png)

这里得到的是一个.sha512的文件，是一种加密文件，但不是源码安装文件。可以直接使用该链接下载`https://artifacts.elastic.co/downloads/logstash/logstash-oss-7.10.2-darwin-x86_64.tar.gz`，仔细的你会发现，`7-10-2`其实就是版本号，如果你安装的不是该版本，直接替换一下就好了。
```shell
// 解压
tar -zxvf logstash-7.10.2-darwin-x86_64.tar.gz

进入可执行目录，下面的文件就是可执行文件
cd /Users/xx/Downloads/logstash-7.10.2/bin && ll
-rw-r--r--   1 kert  staff   221  1 13  2021 benchmark.bat
-rwxr-xr-x   1 kert  staff   152  1 13  2021 benchmark.sh
-rwxr-xr-x   1 kert  staff   377  1 13  2021 cpdump
-rwxr-xr-x   1 kert  staff  1025  1 13  2021 dependencies-report
-rw-r--r--   1 kert  staff   225  1 13  2021 ingest-convert.bat
-rwxr-xr-x   1 kert  staff   155  1 13  2021 ingest-convert.sh
-rwxr-xr-x   1 kert  staff  2287  1 13  2021 logstash
-rwxr-xr-x   1 kert  staff   357  1 13  2021 logstash-keystore
-rw-r--r--   1 kert  staff   257  1 13  2021 logstash-keystore.bat
-rwxr-xr-x   1 kert  staff   358  1 13  2021 logstash-plugin
  staff  2442  1 13  2021 logstash.bat
-rwxr-xr-x   1 kert  staff  5876  1 13  2021 logstash.lib.sh
-rwxr-xr-x   1 kert  staff  1124  1 13  2021 pqcheck
-rw-r--r--   1 kert  staff   475  1 13  2021 pqcheck.bat
-rwxr-xr-x   1 kert  staff  1125  1 13  2021 pqrepair
-rw-r--r--   1 kert  staff   476  1 13  2021 pqrepair.bat
-rwxr-xr-x   1 kert  staff   623  1 13  2021 ruby
-rw-r--r--   1 kert  staff  1768  1 13  2021 setup.bat
```
任意创建一个`.conf`文件， 我这里名字就叫`log.conf`。我这里以Laravel日志为例，向`log.conf`写入如下的内容：
```shell
input {
   file {
      path => ["你laravel日志目录/laravel.log"]
    }
  }
output {
   elasticsearch {
      hosts => ["http://127.0.0.1:9200"]
      index => "lumen-log"
   }
}
```
然后执行`logstash log.conf`就可以了，正常的情况下会看到这样的内容：
```shell
[2022-02-22T00:32:17,071][INFO ][logstash.javapipeline    ][main] Pipeline started {"pipeline.id"=>"main"}
[2022-02-22T00:32:17,151][INFO ][filewatch.observingtail  ][main][7dda28e49683b585e296fde30e6578464882b8bfd26fca79cbad0d53581f074c] START, creating Discoverer, Watch with file and sincedb collections
[2022-02-22T00:32:17,165][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2022-02-22T00:32:17,582][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
```
执行完之后，过一会elasticsearch，里面应该就有数据了。我们依旧使用`ElasticHD`进行查看。

![Snipaste_2022-02-23_23-15-00](http://qiniucloud.qqdeveloper.com/1645629362176-Snipaste_2022-02-23_23-15-00.png)

## 总结

上面的步骤就演示完，如何安装ELK。其实都很简单。最重要的是要注意版本号一致。

![](https://qiniucloud.qqdeveloper.com/public_image.png)