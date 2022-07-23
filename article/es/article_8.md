## 安装说明

Kibana 是一个免费且开放的用户界面，能够让您对 Elasticsearch 数据进行可视化，并让您在 Elastic Stack 中进行导航。您可以进行各种操作，从跟踪查询负载，到理解请求如何流经您的整个应用，都能轻松完成。本文只总结在Linux系统中如何安装。
> kibana的安装一定要和elasticsearch的版本保持一致。

## 安装流程

1. 下载对应的kibana版本(7.10.2)，[下载地址](https://artifacts.elastic.co/downloads/kibana/kibana-7.10.0-linux-x86_64.tar.gz.sha512)

2. 解压源码文件。

```shell
tar -zxvf kibana-7.10.0-linux-x86_64.tar.gz
```

3. 修改连接elasticsearch配置信息。默认的配置文件为`config/kibana.yml`。

```shell
# 服务端口号
server.port: 5601
# 主机地址
server.host: "0.0.0.0"
# elasticsearch账户名称
elasticsearch.username: "hmp"
# elasticsearch账户密码
elasticsearch.password: "hmppass"
# elasticsearch对应的服务地址
elasticsearch.hosts: ["http://localhost:9200"]
```
> 如果elasticsearch默认安装的情况下，上面几项是可以不用单独配置，直接启动kibana服务于即可。

4. 启动kibana服务。

```shell
./bin/kibana
```
> 通过上面的命令，kibana服务就正常启动。然后根据对应的IP地址，访问即可。访问地址应该是`ip`+`端口号(默认5601)`。