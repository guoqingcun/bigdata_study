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

1. 创建索引

   ```java
   /**
   put请求方式 
   http://127.0.0.1:9200/shopping
   */
   ```

   

2. 创建文档

   ```java
   /**
   post请求方式
   http://127.0.0.1:9200/shopping/_doc
   
   json参数
   {
       "title":"华为手机",
       "category":"华为",
       "price":6000
   }
   */
   ```

   

3. 全量数据修改

   ```java
   /**
   put请求方式
   http://127.0.0.1:9200/shopping/_doc/1
   参数
   {
       "title":"小米手机",
       "category":"手机",
       "price":"6000"
   }
   ```

4. 局部修改

   ```java
   /**
   post请求方式
   http://127.0.0.1:9200/shopping/_update/1
   参数
   {
       "doc": {
           "category": "小米品牌"
       }
   }
   */
   ```

   

5. 删除

   ```java
   /**
   delete请求方式
   http://127.0.0.1:9200/shopping/_doc/1
   */
   ```

   

6. 条件查询

   ```java
   /**
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
   注：match默认是模糊查询，查询时会把关键字拆分查询,例:"小米"分拆成"小"和"米"
   */
   ```

   

7. 分页查询

   ```java
   /**
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
   */
   ```

   

8. 排序查询

   ```java
   /**
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
   */
   ```

   

9. 多条件查询

   ```java
   /**
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
   */
   ```

   

10. 范围查询

    ```java
    /**
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
    */
    ```

    

11. 完全匹配

    ```java
    /**
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
    */
    ```

    

12. 高亮查询

    ```java
    /**
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
    */
    ```

    

13. 聚合查询-分组

    ```java
    /**
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
    */
    ```

    

14. 聚合查询-平均值

    ```java
    /**
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
    */
    ```

    

15. 删除索引

    ```java
    /**
    delete请求方式
    http://127.0.0.1:9200/shopping
    */
    ```

    





