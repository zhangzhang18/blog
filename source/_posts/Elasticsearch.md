---
title: Elasticsearch
copyright: true
abbrlink: d32d37e5
date: 2018-12-06 14:31:02
categories:
  - NoteBook
tags:
  - ES
top:
---
[elasticsearch权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_document_oriented.html)

**面向文档**
在应用程序中对象很少只是一个简单的键和值的列表。通常，它们拥有更复杂的数据结构，可能包括日期、地理信息、其他对象或者数组等。

Elasticsearch是面向文档(document oriented)的，这意味着它可以存储整个对象或文档(document)。然而它不仅仅是存储，还会索引(index)每个文档的内容使之可以被搜索。在Elasticsearch中，你可以对文档（而非成行成列的数据）进行索引、搜索、排序、过滤。这种理解数据的方式与以往完全不同，这也是Elasticsearch能够执行复杂的全文搜索的原因之一。

**JSON**
Elasticsearch 使用 JavaScript Object Notation 或者 JSON 作为文档的序列化格式。

Elasticsearch集群可以包含多个索引(indices)（数据库），每一个索引可以包含多个类型(types)（表），每一个类型包含多个文档(documents)（行），然后每个文档包含多个字段(Fields)（列）。
<!-- more -->

所以为了创建员工目录，我们将进行如下操作：


为每个员工的文档(document)建立索引，每个文档包含了相应员工的所有信息。

每个文档的类型为employee。

employee类型归属于索引megacorp。

megacorp索引存储在Elasticsearch集群中。

http://git.daojia-inc.com/jiazheng/jzup-brain.git
实际上这些都是很容易的（尽管看起来有许多步骤）。我们能通过一个命令执行完成的操作：
`PUT /megacorp/employee/1`
```json
{
	"first_name": "John",
	"last_name": "Smith",
	"age": 25,
	"about": "I love to go rock climbing",
	"interests": ["sports", "music"]
}
```
我们看到path:/megacorp/employee/1包含三部分信息：名字说明megacorp索引名employee类型名1这个员工的ID



我们通过HTTP方法GET来检索文档，同样的，我们可以使用DELETE方法删除文档，使用HEAD方法检查某文档是否存在。如果想更新已存在的文档，我们只需再PUT一次。

我们尝试一个最简单的搜索全部员工的请求：

`GET /megacorp/employee/_search`

这种方法常被称作查询字符串(query string)搜索，因为我们像传递URL参数一样去传递查询语句：

`GET /megacorp/employee/_search?q=last_name:Smith`



使用DSL语句查询
DSL(Domain Specific Language特定领域语言)以JSON请求体的形式出现。我们可以这样表示之前关于“Smith”的查询:

`GET /megacorp/employee/_search`
```json
{
	"query": {
		"match": {
			"last_name": "Smith"
		}
	}
}
```
这会返回与之前查询相同的结果。



更复杂的搜索

我们的语句将添加过滤器(filter),它使得我们高效率的执行一个结构化搜索：

`GET /megacorp/employee/_search`
```json
{
	"query": {
		"filtered": {
			"filter": {
				"range": {
					"age": {
						"gt": 30 //1
					}
				}
			},
			"query": {
				"match": {
					"last_name": "smith"//2
				}
			}
		}
	}
}
```
<1> 这部分查询属于区间过滤器(range filter),它用于查找所有年龄大于30岁的数据——gt为"greater than"的缩写。

<2> 这部分查询与之前的match语句(query)一致。



现在我们的搜索结果只显示了一个32岁且名字是“Jane Smith”的员工：
全文搜索
到目前为止搜索都很简单：搜索特定的名字，通过年龄筛选。让我们尝试一种更高级的搜索，全文搜索——一种传统数据库很难实现的功能。
我们将会搜索所有喜欢“rock climbing”的员工：

`GET /megacorp/employee/_search`
```json
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```

解释了Elasticsearch如何在各种文本字段中进行全文搜索，并且返回相关性最大的结果集。相关性(relevance)的概念在Elasticsearch中非常重要，而这个概念在传统关系型数据库中是不可想象的，因为传统数据库对记录的查询只有匹配或者不匹配。


短语搜索
目前我们可以在字段中搜索单独的一个词，这挺好的，但是有时候你想要确切的匹配若干个单词或者短语(phrases)。例如我们想要查询同时包含"rock"和"climbing"（并且是相邻的）的员工记录。
要做到这个，我们只要将match查询变更为match_phrase查询即可:

`GET /megacorp/employee/_search`
```json
{
	"query": {
		"match_phrase": {
			"about": "rock climbing"
		}
	}
}
```


高亮我们的搜索
很多应用喜欢从每个搜索结果中高亮(highlight)匹配到的关键字，这样用户可以知道为什么这些文档和查询相匹配。在Elasticsearch中高亮片段是非常容易的。
让我们在之前的语句上增加highlight参数：

`GET /megacorp/employee/_search`
```json
{
	"query": {
		"match_phrase": {
			"about": "rock climbing"
		}
	},
	"highlight": {
		"fields": {
			"about": {}
		}
	}
}
```
当我们运行这个语句时，会命中与之前相同的结果，但是在返回结果中会有一个新的部分叫做highlight，这里包含了来自about字段中的文本，并且用<em></em>来标识匹配到的单词。