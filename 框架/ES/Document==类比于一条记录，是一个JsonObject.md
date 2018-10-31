Document==>类比于一条记录，是一个JsonObject。有唯一id，不指定的话对自动产生

Index ==>类比于表

每个index都有mapping定义(类似于数据库的schma)

可以使用  curl 或者 Kibana DevTools去调用ES的RESTful API

文档的元数据

```json
 "_index": ".kibana",   doc所在索引
 "_type": "doc",        doc所在类型
 "_id": "config:6.4.1", doc的ID   
 "_version":1           版本号，用于CAS的判断
 "_source": {}          doc原始Json数据
        
```



索引API

```
PUT  /索引名
GET  _cat/indices
DELETE /索引名
```

文档API

```
#### 创建 ####
PUT /索引名/doc/{=id
{
    data
}
POST /索引名/doc
{  
	data
}
ID不指定，自动生成

###  查询 ###
GET  /索引名/doc/id
GET  /索引名/doc/_search{条件}  条件不写会查询所有的

_bulk可以创建多个文档
```

```
cluster.name
node.name
network.host
http.port
path.data
path.log
```



**倒排索引**

倒序索引包括了`单词列表`和`倒排列表`

单词列表记录了所有的doc的分词结果，通过B+树存储。每一个单词和一个倒排列表相关联。

倒排列表由倒排索引项组成。记录了每一个检索词在文档中的信息。包括

文档Id、单词的频率、位置、偏移

ES中存储的是Json，对于每一个Json项都会建立一个倒排索引

Anaslyse API(测试分词结果)
---

```
## 直接去测试一个词的分词
POST _analyse
{
    "analyzer":"",
    "text":""
}
## 测试索引的某一个字段
POST 索引名称/_analyse
{
    "field":"",
    "text":""
}
```

系统内置了很多的分词器，可以根据需要去选择，也可以完全自定义，在分词的不同阶段使用不同的工具。

自定义Mapping
---

```Json
PUT index_name
{
    "mappings":{
        "doc":{
            "dynamic":false,
            "properities":{
                "title":{
                    "type":"text"，
                    "index":true,
                    "index_options":"",
                    "null_value":"值为null的时候存入的默认值"
                },
                "age":{
                    "type":"integer"
                }
            }
        }
    }
}
```

mapping一旦定义之后就不允许修改了，但是可以新增字段。
dynamic取值为`true`，则有新的document插入的新的字段的时候，自动建立倒排索引。
取值为`false`,则可以插入新的字段，但是无法对新的字段进行查询操作
取值为`strict`则多余字段的文档不能写入，直接报错

`index`取值表示是否建立索引，默认`true`建立索引，`false`不建立索引(不能搜索)

`index_options`表明索引需要记录那些信息

数据类型
---

不创建索引，直接插入doc的时候，es会自动创建一个索引，索引对应的类型通过dynamic mapping自行进行判断

