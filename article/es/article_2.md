## 索引操作

### 创建索引

向 ES 服务器发 PUT 请求 ： `http://127.0.0.1:9200/shopping`。创建索引只能使用PUT请求，PUT是幂等性的，也就是说不存在的时候就会创建，存在的时候就不会重新创建而是返回索引已经存在的信息。
```php
{
    "acknowledged": true,//响应结果
    "shards_acknowledged": true,//分片结果
    "index": "shopping"//索引名称
}
```

### 查询索引

向 ES 服务器发 GET 请求 ： `http://127.0.0.1:9200/shopping`。
```php
{
    "shopping": {//索引名
        "aliases": {},//别名
        "mappings": {},//映射
        "settings": {//设置
            "index": {//设置 - 索引
                "creation_date": "1617861426847",//设置 - 索引 - 创建时间
                "number_of_shards": "1",//设置 - 索引 - 主分片数量
                "number_of_replicas": "1",//设置 - 索引 - 主分片数量
                "uuid": "J0WlEhh4R7aDrfIc3AkwWQ",//设置 - 索引 - 主分片数量
                "version": {//设置 - 索引 - 主分片数量
                    "created": "7080099"
                },
                "provided_name": "shopping"//设置 - 索引 - 主分片数量
            }
        }
    }
}
```

### 查看所有索引

向 ES 服务器发 GET 请求 ： `http://127.0.0.1:9200/_cat/indices?v`。

这里请求路径中的_cat 表示查看的意思， indices 表示索引，所以整体含义就是查看当前 ES服务器中的所有索引，就好像 MySQL 中的 show tables 的感觉，服务器响应结果如下 :
```php
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   shopping J0WlEhh4R7aDrfIc3AkwWQ   1   1          0            0       208b           208b
```

### 删除索引

向 ES 服务器发 DELETE 请求 ： `http://127.0.0.1:9200/shopping`。

返回结果如下：
```php
{
    "acknowledged": true
}
```