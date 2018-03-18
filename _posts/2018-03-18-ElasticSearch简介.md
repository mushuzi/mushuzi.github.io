---
layout:     post
title:      ElasticSearch简介
subtitle:   ElasticSearch简介
date:       2018-03-18
author:     mushuzi
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
   - ElasticSearch
   - MySQL
---

# 前言
最近在项目开发过程中遇到这样的问题：项目使用MySQL数据库，每次采用MySQL查询的方式编写查询语句，并从数据库中进行查询。然而，在数据规模较大时，这种查询方式的查询效率很低。于是，为了提高查询效率，项目中我们实现了MySQL与ElasticSearch的整合。那么，ElasticSearch到底是什么呢？

# 正文
ElasticSearch是一个使用Java开发的基于Apache  Lucene的开源搜索引擎，但相比于复杂的Lucene而言，ElasticSearch使用Restful API隐藏了Lucene的复杂性，从而使全文搜索变得简单。对于ElasticSearch，我们可以这样来描述。

* 分布式的实时文件存储，每个字段都被索引并可被搜索
* 分布式的实时分析搜索引擎
* 可以扩展到上百台服务器，处理PB级结构化或非结构化数据

所有的这些功能都被继承到一个服务里面，应用可以通过简单的Restful API，使用各种语言甚至命令行进行交互。

Elastic 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。我们来看ElasticSearch中几个基本的概念：

* 分片（shard）：当有大量的文档时，由于内存的限制、磁盘处理能力不足、无法足够快的响应客户端的请求等问题，一个节点可能不够。这种情况下，数据可以分为较小的分片。每个分片放到不同的服务器上。当你查询的索引分布在多个分片上时，ES会把查询发送给每个相关的分片，并将结果组合在一起，而应用程序并不知道分片的存在。即：这个过程对用户来说是透明的。

* 索引（index）：是ElasticSearch数据管理的顶层单位，类似于MySQL中的数据库名。ElasticSearch会索引所有字段，查找数据的时候，直接查找该索引。

* 类型（type）：1个索引下面有N个类型，等价于MySQL中一个数据库下有N张表。即类型对应于MySQL数据库中的表（Table）。

* 文档（document）：ElasticSearch里面的单条记录叫做文档，多条文档构成一个index。文档类似于MySQL中的记录，即MySQL数据库表中的行（Row）的概念。举个例子：使用JSON表示的一个文档为{"website":"56", "docId": "01", "dt" : "2017-01-09"}。同一个index下的文档，并不要求有相同的结构，但最好结构相同，因为这样更有利于提高搜索效率。

* 字段（Field）：每一个文档都有若干个字段组成，字段类似于MySQL数据库中的列（Column）。

# 应用
ElasticSearch支持索引、查询、排序、聚合等一系列操作。下来我们举一个常见的例子，结合MySQL的使用来加以介绍：

我们在MySQL数据中，经常会使用以下查询语句：

    String query = "select website, docId, name, sum(clickCount) as clickCount, dt from TableName where website = "baidu" and dt = "2017-01-09" group by docId order by clickCount desc"

我们来看在ElasticSearch中怎么构建上述查询语句(我们采用闭包的方式来表示）：

    def query = {
       fields = [website, docId, name, clickCount, dt]
       query {
          filtered {
             filter {
                bool {
                   must {
                      term (website : "baidu")
                      match_phase (dt : "2017-01-09")
                   }
                }
             }
          }
       }
       aggs {
          "docId" {terms {field :"docId"}
                aggs {
                      "totalClickCount" {sum {field : "clickCount"}}
                }
            }
       }
       sort = {"clickCount" : {order = "desc"}}
    }

当前上述例子只是在方法上给出了使用ElasticSearch构建查询语句的方式，更多更细致的具体的细节还需要依据相应的参考文档进行细化。

# ElasticSearch相关参考网站
ElasticSearch全为指南：https://es.xiaoleilu.com/

http://blog.csdn.net/laoyang360/article/details/52244917

ElasticSearch插件文档：https://noamt.github.io/elasticsearch-grails-plugin/snapshot/index.html#configuration

ElasticSearch groovy插件文档：https://noamt.github.io/elasticsearch-grails-plugin/snapshot/ref/index.html
