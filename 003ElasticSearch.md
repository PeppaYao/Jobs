## ES原理

### 1、概念

ES是一个分布式，基于Restful风格的数据搜索和分析引擎。



### 2、倒排索引

倒排索引表中每一项都包括一个属性值和具有该属性值对应记录的地址。

由于不是按照记录来确定属性值，而是由属性值来确定记录的位置，因此被称为倒排索引。



### 3、 ES集群底层

健康状态：

**Green/Yellow/Red**

-   Green：所有主分片和副分片都运行正常。
-   Yellow：所有主分片都运行正常，但至少有一个副本分片运行异常。
-   Red：至少有一个主分片运行异常。

节点类型：

- master node：主节点主要负责集群节点状态的维护，索引的创建删除，数据的Rebalance，分片的分配等工作。
- data node：数据节点主要负责集群中数据的索引创建和检索，具体操作包括数据的索引，检索，聚合等。
- Coordinating node：协调节点用于接收客户端请求，并将请求转发到各个DataNode。
- ingest node：提取节点是执行数据预处理的管道，它在索引之前预处理文档。
- TribeNode：部落节点允许在多个集群之间充当联合客户端，用于实现跨集群访问。

实例

一个集群由一个或多个节点组织在一起，它们共同持有整个集群的数据，并一起提供索引和搜索功能。

一个节点是集群中的一个服务器。

**分片：默认分片为5片，全加起来才是一个整体，每片数据都不一样。**

一个物理的存储单元是一个luence创建的索引库。

一个Shard分片就是一个Lucene Index。 

![1597392168326](C:\Users\Yao\AppData\Local\Temp\1597392168326.png)

### 4、Windows上使用ES

```java
安装可视化界面head
npm install  前提时安装好了nodejs
npm run start
跨域问题
http.cros.enabled: true
http.cros.allow-origin: "*"
```

在plugins目录下加载ik分词器和拼音分词器



### 5、Jsoup的使用

爬取数据：Jsoup中getElementsByClass中className有空格处理，取空格中的可以唯一标识的class即可。

```Java
Document document = Jsoup.parse(new URL(url), 300000);
```

基本代码：获取每个用户写的一篇博客

```java
package com.bytedance;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.IOException;

public class HtmlParseUtilSecond {
    public static void ParseArticle(String uri) throws IOException {
        String url = "https://www.nowcoder.com/" + uri;
        Document document = Jsoup.connect(url).get();
        Elements content = document.select("div.nc-post-content");
        System.out.println("===================================");
        for (Element element : content) {
            int total = element.childrenSize();
            for (int i = 0; i < total; i++) {
                System.out.println(element.child(i).text());  //输出一行的内容

            }
        }
        System.out.println("===================================");
    }
}

```

基本代码：获取讨论区

```java
package com.bytedance;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.IOException;

import static com.bytedance.HtmlParseUtilSecond.ParseArticle;

public class HtmlParseUtilFirst {
    public static void main(String[] args) throws IOException {
         //美团后端
        String url = "https://www.nowcoder.com/search?type=post&order=time&query=%E7%BE%8E%E5%9B%A2%E5%90%8E%E7%AB%AF&subType=2&tagId=&page=1";
        // 字节后端String url = "https://www.nowcoder.com/search?type=post&tagId=862&query=%E5%AD%97%E8%8A%82%E8%B7%B3%E5%8A%A8%2B%E5%90%8E%E7%AB%AF%2Boc%2Boffer&subType=2";
        Document document = Jsoup.connect(url).get();
        Elements content = document.select("ul.common-list");

        for (Element element : content) {
            Elements select = element.select("li.clearfix");
            for(Element detail: select){
                Elements select1 = detail.select("div.discuss-detail");
               for(Element e: select1){
                   Elements a = e.getElementsByTag("a");
                   String href = a.attr("href");
                   ParseArticle(href);
               }
            }
        }
    }
}

```

## 6、使用Kibana操作

```json
GET /_cat/indices

GET yaojun/_search
{
  
}

POST yaojun/111
{
  "name":"yaojun",
  "age":18
}
```

### 7、集群选举

ES集群选主操作采用bully算法。

### 8、脑裂现象

指在分布式集群中节点之间犹豫网络分区而不能正常通信，是的原本为一个而整体的集群分裂为两个或多个集群，从而导致系统混乱，服务异常，数据不一致的现象。为防止脑裂的发生，ES通过discovery.zen.minimum_master_nodes来控制选主的条件。

### 9、数据路由规则

用于确定文档存储在哪个索引的哪个分片上。

```json
shard = hash(routing) % number_of_primary_shards
```

文档所在分片等于routing的hash值除以主分片数量的余数

### 10、Translog

ES的事务日志文件为Translog，记录了所有与更新相关的事物操作日志。

