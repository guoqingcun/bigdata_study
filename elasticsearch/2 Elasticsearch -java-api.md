# 使用maven创建一java项目

## 环境

- jdk1.8
- Intellij ideal 2020.1.2



## pom.xml

```java
        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>7.13.0</version>
        </dependency>
        <!--elasticsearch客户端-->
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.13.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.14.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.14.1</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.12.5</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.20</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>test</scope>
        </dependency>
```



## 代码

- **创建索引**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.CreateIndexResponse;

import java.io.IOException;

public class ESCreateIndices {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        CreateIndexRequest request = new CreateIndexRequest("user");
        CreateIndexResponse createIndexResponse = client.indices().create(request, RequestOptions.DEFAULT);
        boolean acknowledged = createIndexResponse.isAcknowledged();
        System.out.println(acknowledged);
        client.close();

    }
}
```

- **索引查询**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;

import java.io.IOException;
public  class ESGetIndices {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        GetIndexRequest request = new GetIndexRequest("user");
        GetIndexResponse getIndexResponse = client.indices().get(request, RequestOptions.DEFAULT);


        System.out.println(getIndexResponse.getAliases());
        client.close();

    }
}
```



- **删除索引**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

public class ESDeleteIndices {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        DeleteIndexRequest request = new DeleteIndexRequest("user");;
        AcknowledgedResponse deleteIndexResponse = client.indices().delete(request, RequestOptions.DEFAULT);


        System.out.println(deleteIndexResponse.isAcknowledged());
        client.close();

    }
}
```

- **文档新增**

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import lombok.Data;
import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

public class ESInsertDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        IndexRequest request = new IndexRequest("user");
        request.id("1");
        User user = new User();
        user.setName("zhangsan");
        user.setSex("男");
        user.setAge(20);

        String userJson = new ObjectMapper().writeValueAsString(user);
        request.source(userJson, XContentType.JSON);

        IndexResponse indexResponse = client.index(request, RequestOptions.DEFAULT);
        System.out.println(indexResponse.status());

        client.close();

    }
}

@Data
class User{
    private String name;
    private String sex;
    private int age;
}
```



- **文档修改**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

public class ESUpdateDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        UpdateRequest request = new UpdateRequest(
                "user",
                "1");
        request.doc(XContentType.JSON,"sex","女");


        UpdateResponse updateResponse = client.update(
                request, RequestOptions.DEFAULT);
        System.out.println(updateResponse.status());

        client.close();

    }
}
```



- **文档查询**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

public class ESGetDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        GetRequest getRequest = new GetRequest(
                "user",
                "1");

        GetResponse getResponse = client.get(getRequest, RequestOptions.DEFAULT);
        System.out.println(getResponse.getSourceAsString());

        client.close();
    }
}
```



- **文档删除**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

public class ESDeleteDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        DeleteRequest request = new DeleteRequest(
                "user",
                "1");

        DeleteResponse deleteResponse = client.delete(
                request, RequestOptions.DEFAULT);
        System.out.println(deleteResponse.status());

        client.close();
    }
}
```



- **批量新增**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

public class ESBatchInsertDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        BulkRequest request = new BulkRequest();
        request.add(new IndexRequest("user").id("1")
                .source(XContentType.JSON,"name", "zhangsan"));
        request.add(new IndexRequest("user").id("2")
                .source(XContentType.JSON,"name", "lisi"));
        request.add(new IndexRequest("user").id("3")
                .source(XContentType.JSON,"name", "wangwu"));


        BulkResponse bulkResponse = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println(bulkResponse.status());

        client.close();

    }
}
```



- **批量删除**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

public class ESBatchDeleDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        BulkRequest request = new BulkRequest();
        request.add(new DeleteRequest("user").id("1"));
        request.add(new DeleteRequest("user").id("2"));
        request.add(new DeleteRequest("user").id("3"));


        BulkResponse bulkResponse = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println(bulkResponse.status());

        client.close();

    }
}
```



- **高级查询**

```java
import org.apache.http.HttpHost;
import org.elasticsearch.action.search.SearchRequest;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.builder.SearchSourceBuilder;

import java.io.IOException;

public class ESSearchAllDoc {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("localhost", 9200, "http")
                ));

        SearchRequest searchRequest = new SearchRequest("user");
        SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
        searchSourceBuilder.query(QueryBuilders.matchAllQuery());
        searchRequest.source(searchSourceBuilder);

        SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT);
        SearchHits hits = searchResponse.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }


        client.close();
    }
}
```

