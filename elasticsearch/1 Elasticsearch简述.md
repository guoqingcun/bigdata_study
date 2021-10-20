# 概念

Elasticsearch 是一个分布式、RESTful 风格的搜索和数据分析引擎。作为 Elastic Stack 的核心，它集中存储您的数据。



# Elasticsearch能做什么

- 查询
  - 通过 Elasticsearch，您能够执行及合并多种类型的搜索（结构化数据、非结构化数据、地理位置、指标）
- 分析
  - 面对的是十亿行日志，探索数据的趋势和规律。



# 倒排索引

倒排索引是正向索引的反意。根据keyword找查询文档.如下例所示：

keyword       文档id

\-----------------------------------------

iphone          1002,1005,1006



# 基本操作

1.  创建索引

   ```json
   put请求方式 
   http://127.0.0.1:9200/shopping
   -----------------------------------
   结果
   {
       "acknowledged": true,
       "shards_acknowledged": true,
       "index": "shopping"
   }
   
   
   
   ```

   

2. 创建文档

   ```json
   post请求方式
   http://127.0.0.1:9200/shopping/_doc
   
   json参数
   {
       "title":"华为手机",
       "category":"华为",
       "price":6000
   }
   -----------------------------
   结果
   {
       "_index": "shopping",
       "_type": "_doc",
       "_id": "UtVxnXwB9IWPXOScIkn7",
       "_version": 1,
       "result": "created",
       "_shards": {
           "total": 2,
           "successful": 1,
           "failed": 0
       },
       "_seq_no": 0,
       "_primary_term": 1
   }
   ```

   

3. 全量数据修改

   ```json
   put请求方式
   http://127.0.0.1:9200/shopping/_doc/1
   参数
   {
       "title":"小米手机",
       "category":"手机",
       "price":"6000"
   }
   -----------------------
   结果
   {
       "_index": "shopping",
       "_type": "_doc",
       "_id": "1",
       "_version": 2,
       "result": "updated",
       "_shards": {
           "total": 2,
           "successful": 1,
           "failed": 0
       },
       "_seq_no": 2,
       "_primary_term": 1
   }
   ```

4. 局部修改

   ```
   post请求方式
   http://127.0.0.1:9200/shopping/_update/1
   参数
   {
       "doc": {
           "category": "小米品牌"
       }
   }
   ---------------------------
   结果
   {
       "_index": "shopping",
       "_type": "_doc",
       "_id": "1",
       "_version": 3,
       "result": "updated",
       "_shards": {
           "total": 2,
           "successful": 1,
           "failed": 0
       },
       "_seq_no": 5,
       "_primary_term": 1
   }
   ```

   

5. 删除

   ```json
   delete请求方式
   http://127.0.0.1:9200/shopping/_doc/1
   --------------------
   结果
   {
       "_index": "shopping",
       "_type": "_doc",
       "_id": "1",
       "_version": 4,
       "result": "deleted",
       "_shards": {
           "total": 2,
           "successful": 1,
           "failed": 0
       },
       "_seq_no": 6,
       "_primary_term": 1
   }
   
   ```

   

6.  条件查询

   ```json
   get请求方式
   http://127.0.0.1:9200/shopping/_search
   参数
   {
       "query":{
           "match":{
               "category":"小米"
           }
       }
   }
   ------------------------
   结果
   {
       "took": 528,
       "timed_out": false,
       "_shards": {
           "total": 1,
           "successful": 1,
           "skipped": 0,
           "failed": 0
       },
       "hits": {
           "total": {
               "value": 1,
               "relation": "eq"
           },
           "max_score": 1.4723402,
           "hits": [
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "VdV2nXwB9IWPXOScYEms",
                   "_score": 1.4723402,
                   "_source": {
                       "title": "小米手机",
                       "category": "小米",
                       "price": "5000"
                   }
               }
           ]
       }
   }
   注：match默认是模糊查询，查询时会把关键字拆分查询,例:"小米"分拆成"小"和"米"
   ```

   

7. 分页查询

   ```json
   get请求方式
   http://127.0.0.1:9200/shopping/_search
   参数
   {
       "query":{
           "match_all":{
               
           }
       },
       "from":0,
       "size":5
   }
   -----------------------------
   结果
   {
       "took": 3,
       "timed_out": false,
       "_shards": {
           "total": 1,
           "successful": 1,
           "skipped": 0,
           "failed": 0
       },
       "hits": {
           "total": {
               "value": 3,
               "relation": "eq"
           },
           "max_score": 1.0,
           "hits": [
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "UtVxnXwB9IWPXOScIkn7",
                   "_score": 1.0,
                   "_source": {
                       "title": "华为手机",
                       "category": "华为",
                       "price": 6000
                   }
               },
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "U9V1nXwB9IWPXOScbEnf",
                   "_score": 1.0,
                   "_source": {
                       "id": 3,
                       "title": "华为手机",
                       "category": "华为",
                       "price": 6000
                   }
               },
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "VdV2nXwB9IWPXOScYEms",
                   "_score": 1.0,
                   "_source": {
                       "title": "小米手机",
                       "category": "小米",
                       "price": "5000"
                   }
               }
           ]
       }
   }
   ```

   

8. 排序查询

   ```json
   get请求方式
   http://127.0.0.1:9200/shopping/_search
   参数
   {
       "query":{
           "match_all":{
               
           }
       },
       "from":0,
       "size":5,
       "sort":{
           "price":{
               "order":"desc"
           }
       } 
   }
   -----------------------
   结果
   {
       "took": 123,
       "timed_out": false,
       "_shards": {
           "total": 1,
           "successful": 1,
           "skipped": 0,
           "failed": 0
       },
       "hits": {
           "total": {
               "value": 3,
               "relation": "eq"
           },
           "max_score": null,
           "hits": [
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "UtVxnXwB9IWPXOScIkn7",
                   "_score": null,
                   "_source": {
                       "title": "华为手机",
                       "category": "华为",
                       "price": 6000
                   },
                   "sort": [
                       6000
                   ]
               },
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "U9V1nXwB9IWPXOScbEnf",
                   "_score": null,
                   "_source": {
                       "id": 3,
                       "title": "华为手机",
                       "category": "华为",
                       "price": 6000
                   },
                   "sort": [
                       6000
                   ]
               },
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "VdV2nXwB9IWPXOScYEms",
                   "_score": null,
                   "_source": {
                       "title": "小米手机",
                       "category": "小米",
                       "price": "5000"
                   },
                   "sort": [
                       5000
                   ]
               }
           ]
       }
   }
   ```

   

9. 多条件查询

   ```json
   get请求方式
   http://127.0.0.1:9200/shopping/_search
   参数
   {
       "query": {
           "bool": {
               "must": [
                   {
                       "match": {
                           "category": "小米"
                       }
                   },
                   {
                       "match": {
                           "price": 5000
                       }
                   }
               ]
           }
       }
   }
   ----------------------------
   结果
   {
       "took": 55,
       "timed_out": false,
       "_shards": {
           "total": 1,
           "successful": 1,
           "skipped": 0,
           "failed": 0
       },
       "hits": {
           "total": {
               "value": 1,
               "relation": "eq"
           },
           "max_score": 2.9616582,
           "hits": [
               {
                   "_index": "shopping",
                   "_type": "_doc",
                   "_id": "VdV2nXwB9IWPXOScYEms",
                   "_score": 2.9616582,
                   "_source": {
                       "title": "小米手机",
                       "category": "小米",
                       "price": "5000"
                   }
               }
           ]
       }
   }
   ```

   

10. 范围查询

    ```json
    get请求方式
    http://127.0.0.1:9200/shopping/_search
    参数
    {
        "query": {
            "bool": {
                "should": [
                    {
                        "match": {
                            "category": "小米"
                        }
                    },
                    {
                        "match": {
                            "category": "华为"
                        }
                    }
                ],
                "filter":{
                    "range":{
                        "price":{
                            "gt":5000
                        }
                    }
                }
            }
        }
    }
    --------------------------
    结果
    {
        "took": 12,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 0.9400072,
            "hits": [
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "UtVxnXwB9IWPXOScIkn7",
                    "_score": 0.9400072,
                    "_source": {
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    }
                },
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "U9V1nXwB9IWPXOScbEnf",
                    "_score": 0.9400072,
                    "_source": {
                        "id": 3,
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    }
                }
            ]
        }
    }
    ```

    

11. 完全匹配

    ```json
    get请求方式
    http://127.0.0.1:9200/shopping/_search
    参数
    {
        "query": {
            "match_phrase":{
                "category":"华为"
            }
        }
    }
    ------------------------
    结果
    {
        "took": 142,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 0.9400072,
            "hits": [
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "UtVxnXwB9IWPXOScIkn7",
                    "_score": 0.9400072,
                    "_source": {
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    }
                },
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "U9V1nXwB9IWPXOScbEnf",
                    "_score": 0.9400072,
                    "_source": {
                        "id": 3,
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    }
                }
            ]
        }
    }
    ```

    

12. 高亮查询

    ```json
    get请求方式
    http://127.0.0.1:9200/shopping/_search
    参数
    {
        "query":{
            "match_phrase":{
                "category":"华为"
            }
        },
        "highlight":{
            "fields":{
                "category":{}
            }
    
        }
    }
    -----------------------
    结果
    {
        "took": 105,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 0.9400072,
            "hits": [
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "UtVxnXwB9IWPXOScIkn7",
                    "_score": 0.9400072,
                    "_source": {
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    },
                    "highlight": {
                        "category": [
                            "<em>华</em><em>为</em>"
                        ]
                    }
                },
                {
                    "_index": "shopping",
                    "_type": "_doc",
                    "_id": "U9V1nXwB9IWPXOScbEnf",
                    "_score": 0.9400072,
                    "_source": {
                        "id": 3,
                        "title": "华为手机",
                        "category": "华为",
                        "price": 6000
                    },
                    "highlight": {
                        "category": [
                            "<em>华</em><em>为</em>"
                        ]
                    }
                }
            ]
        }
    }
    ```

    

13. 聚合查询-分组

    ```json
    get请求方式
    http://127.0.0.1:9200/shopping/_search
    参数
    {
        "aggs":{
            "price_group":{
                "terms":{
                    "field":"price"
    
                }
            }
        },
        "size":0
    }
    ---------------------
    结果
    {
        "took": 28,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 3,
                "relation": "eq"
            },
            "max_score": null,
            "hits": []
        },
        "aggregations": {
            "price_group": {
                "doc_count_error_upper_bound": 0,
                "sum_other_doc_count": 0,
                "buckets": [
                    {
                        "key": 6000,
                        "doc_count": 2
                    },
                    {
                        "key": 5000,
                        "doc_count": 1
                    }
                ]
            }
        }
    }
    ```

    

14. 聚合查询-平均值

    ```json
    get请求方式
    http://127.0.0.1:9200/shopping/_search
    参数
    {
        "aggs":{
            "price_avg":{
                "avg":{
                    "field":"price"
    
                }
            }
        },
        "size":0
    }
    --------------------------
    {
        "took": 14,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 3,
                "relation": "eq"
            },
            "max_score": null,
            "hits": []
        },
        "aggregations": {
            "price_avg": {
                "value": 5666.666666666667
            }
        }
    }
    ```

    

15. 删除索引

    ```json
    delete请求方式
    http://127.0.0.1:9200/shopping
    ---------------------------
    结果
    {
        "acknowledged": true
    }
    ```

    





