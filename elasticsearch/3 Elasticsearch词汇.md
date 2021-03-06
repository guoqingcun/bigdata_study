# Elasticsearch词汇



**索引-index**

- 索引是文档集合。比如用户索引、产品索引、订单索引。一个索引由一个名字来标识，必须全部是小写字母。在一个集群中，可以定义任意多的索引。
- elasticsearch索引的精髓：一切设计都是为了提高搜索的性能。



**文档-document**

- 一个文档就是一个可被索引的信息单元，也就是一条记录。
- 一个索引里面，可以存储任意多的文档。



**字段-field**

- 相当于数据表字段



**映射-mapping**

- mapping是处理数据的方式和规则方面做一些限制，如：某个字段的数据类型、默认值、分析器、是否被索引等等。这些都是映射里面可以设置的，其它就是处理ES里面数据的一些使用规则设置也叫做映射，按着最优规则处理数据对性能提高很大，因此才需要建立映射，并且需要思考如何建立映射才能对性能更好。



**分片-shard**

- 一个索引可以存储超过单个节点硬件限制的大量数据。比如，一个具有10亿文档数据的索引占据1TB的磁盘空间，而任一节点都可能没有这样大的磁盘空间。或单个节点处理搜索请求，响应太慢。为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，每一份就称之为分片。当你创建索引的时候，可以指定分片数量。每个分片本身也是一个功能完善并且独立的索引。

- 分片两个重要点

- - 允许水平分割扩展内容容量。
  - 允许分片上进行分布式、并行操作，进而提高性能/吞吐量。



**副本-replicas**

- 为实现系统高可用，elasticsearch允许创建分片的一份或者几份拷贝，这些拷贝叫做复制分片，也就是副本。

- 副本两个重要点

- - 高可用性
  - 提高吞吐量



**分配-allocation**

- 将分片分配给某个节点的过程，包括分配主分片或者副本。如果是副本，还包含从主分片复制数据的过程，这个过程是由master节点完成的。

