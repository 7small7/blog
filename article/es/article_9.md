## 安装说明

Logstash 是免费且开放的服务器端数据处理管道，能够从多个来源采集数据，转换数据，然后将数据发送到您最喜欢的“存储库”中。本文只总结在Linux系统中如何安装。
> kibana的安装一定要和elasticsearch的版本保持一致。

## 安装流程

1. 下载对应的logstash版本(7.10.2)，[下载地址](https://artifacts.elastic.co/downloads/logstash/logstash-7.10.0-linux-x86_64.tar.gz.sha512)

2. 解压源码文件。

```shell
tar -zxvf logstash-7.10.0-linux-x86_64.tar.gz
```

3. 需要启动logstash服务。

```shell
cd bin/logstash
```

## 测试文件

1. 任意创建一个`.conf`文件， 我这里名字就叫`demo.conf`。我这里以Laravel日志为例，向`demo.conf`写入如下的内容：

```shell
input {
   file {
      path => ["/op/logstash/demo.log"]# 需要收集的日志文件路径。
    }
  }
output {
   elasticsearch {
      hosts => ["http://127.0.0.1:9200"]# es地址
      index => "lumen-log"# es索引名称
   }
}
```
> demo.log文件，在演示中是直接复制Nginx的error.log文件。

1. 然后执行`logstash -f demo.conf`就可以了，正常的情况下会看到这样的内容：

```shell
[2022-07-23T21:00:30,380][INFO ][logstash.outputs.elasticsearch][main] Installing elasticsearch template to _template/logstash
[2022-07-23T21:00:31,486][INFO ][logstash.javapipeline    ][main] Pipeline Java execution initialization time {"seconds"=>1.19}
[2022-07-23T21:00:31,860][INFO ][logstash.inputs.file     ][main] No sincedb_path set, generating one based on the "path" setting {:sincedb_path=>"/opt/logstash/data/plugins/inputs/file/.sincedb_130b36a695bca9015d077fb525c9ac21", :path=>["/opt/logstash/demo.log"]}
[2022-07-23T21:00:31,892][INFO ][logstash.javapipeline    ][main] Pipeline started {"pipeline.id"=>"main"}
[2022-07-23T21:00:31,923][INFO ][filewatch.observingtail  ][main][61d48e63acf660295f0aabd6322d467c27444901b5bd739c2c705199a86b9fc1] START, creating Discoverer, Watch with file and sincedb collections
[2022-07-23T21:00:31,940][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2022-07-23T21:00:32,311][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}

```
> 执行完之后，过一会elasticsearch，里面应该就有数据了。

3. 测试日志示例。

```shell
2022/07/17 21:50:19 [crit] 27906#0: *1085 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 185.183.106.147, server: 0.0.0.0:443
2022/07/17 21:53:18 [crit] 28317#0: *1118 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 107.178.200.201, server: 0.0.0.0:443
2022/07/17 23:03:19 [crit] 28317#0: *6369 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 107.178.200.205, server: 0.0.0.0:443
2022/07/18 04:55:21 [crit] 28319#0: *6621 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 106.75.171.101, server: 0.0.0.0:443
2022/07/18 06:32:08 [crit] 28317#0: *6670 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 192.241.205.140, server: 0.0.0.0:443
2022/07/18 06:58:26 [crit] 28318#0: *6713 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 124.220.186.134, server: 0.0.0.0:443
2022/07/18 14:57:42 [crit] 28318#0: *6904 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 184.105.247.196, server: 0.0.0.0:443
2022/07/19 08:14:07 [crit] 28317#0: *7794 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 119.90.62.22, server: 0.0.0.0:443
2022/07/20 05:22:08 [crit] 28318#0: *8374 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 167.172.240.54, server: 0.0.0.0:443
2022/07/20 14:06:18 [crit] 28317#0: *9062 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 192.241.213.116, server: 0.0.0.0:443
2022/07/20 22:41:29 [crit] 28317#0: *9567 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 74.82.47.4, server: 0.0.0.0:443
2022/07/21 16:31:52 [crit] 11903#0: *10588 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 167.248.133.62, server: 0.0.0.0:443
2022/07/21 19:54:32 [crit] 11902#0: *10761 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 154.89.5.69, server: 0.0.0.0:443
2022/07/21 21:17:59 [crit] 11905#0: *10806 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 64.62.197.227, server: 0.0.0.0:443
2022/07/22 01:56:21 [crit] 11905#0: *10894 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 161.35.86.181, server: 0.0.0.0:443
2022/07/22 09:06:45 [crit] 11903#0: *11040 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 185.142.236.43, server: 0.0.0.0:443
2022/07/22 12:10:24 [crit] 11905#0: *11234 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 178.73.215.171, server: 0.0.0.0:443
2022/07/22 14:00:37 [crit] 11904#0: *11272 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 64.62.197.92, server: 0.0.0.0:443
2022/07/23 02:53:34 [crit] 11905#0: *12219 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 143.198.136.88, server: 0.0.0.0:443
2022/07/23 13:28:20 [crit] 11904#0: *12468 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 27.124.5.24, server: 0.0.0.0:443
2022/07/23 18:24:36 [crit] 11905#0: *12574 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 184.105.139.70, server: 0.0.0.0:443
2022/07/23 19:02:08 [crit] 11904#0: *12601 SSL_do_handshake() failed (SSL: error:141CF06C:SSL routines:tls_parse_ctos_key_share:bad key share) while SSL handshaking, client: 51.159.99.249, server: 0.0.0.0:443
```
> 上面的内容直接复制到demo.log就可以直接使用。

## 查看效果

通过上面的日志导入，此时可以直接到kibana中进行查看。



