#  Spring-Boot集成Elasticsearch

### 环境

- jdk1.8
- itellij ideal 2020.1.2



### 项目

> 创建一spring-boot项目

#### pom.xml

```xml
<!--必须-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
<!--以下非必须-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

####  配置文件(可选)

> 自动装配中默认ES地址与我们如下定义内容相同

```yaml
spring:
  elasticsearch:
    rest:
      uris: http://192.168.1.9:9200
logging:
  level:
    cn.onlon: debug
```



#### 代码

> entity类：Product

```java
package cn.onlon.student.es.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;
import org.springframework.data.elasticsearch.annotations.Setting;

@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
@Document(indexName = "product")
@Setting(shards = 1,replicas = 1)
public class Product {
    @Id
    private  Long id;
//    @Field(type = FieldType.Text,analyzer = "ik_max_word")
    @Field(type = FieldType.Text)
    private  String title;
    @Field(type= FieldType.Keyword)
    private  String category;
    @Field(type= FieldType.Double)
    private  Double price;
    @Field(type= FieldType.Keyword,index = false)
    private  String images;
}
```

> dao层对象

```java
package cn.onlon.student.es.dao;

import cn.onlon.student.es.entity.Product;
import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProductDao extends ElasticsearchRepository<Product,Long> {

}
```

> 测试类

```java
package cn.onlon.student.es;

import cn.onlon.student.es.dao.ProductDao;
import cn.onlon.student.es.entity.Product;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.data.elasticsearch.core.ElasticsearchRestTemplate;

import java.util.ArrayList;
import java.util.List;

@SpringBootTest
class SpringBootEsApplicationTests {

    @Autowired
    private ElasticsearchRestTemplate template;

    @Autowired
    private ProductDao productDao;

    @Test
    void contextLoads() {
    }

    /**
     * 创建索引
     */
    @Test
    public void createIndex(){
        System.out.println("创建索引");
    }

    /*
    失效
    @Test
    public void deleteIndex(){
        String delete = template.delete(new Product());
        System.out.println("删除索引："+delete);
    }
    */

    @Test
    public void save(){
        productDao.save(new Product(1l, "华为手机好", "手机", 1000d, "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fmp.ofweek.com%2Fdata%2Fimages%2Fim2maker%2F2020-04-09%2F5dafbb0ab0040d34523c1e19beb732eb.png&refer=http%3A%2F%2Fmp.ofweek.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1637229208&t=5eb27b8579ce2916b7f6257707772fb0"));
        System.out.println("保存成功");
    }

    @Test
    public void update(){
        productDao.save(new Product(1l, "小米手机", "手机", 1000d, "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fmp.ofweek.com%2Fdata%2Fimages%2Fim2maker%2F2020-04-09%2F5dafbb0ab0040d34523c1e19beb732eb.png&refer=http%3A%2F%2Fmp.ofweek.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1637229208&t=5eb27b8579ce2916b7f6257707772fb0"));
        System.out.println("更新成功");
    }

    @Test
    public void get(){
        Product product = productDao.findById(1l).get();

        System.out.println(product);
    }

    @Test
    public void getAll(){
        Iterable<Product> all = productDao.findAll();
        all.forEach(System.out::println);
//        System.out.println(product);
    }

    @Test
    public void deleteById() {

        Product product = new Product();
        product.setId(1l);
        productDao.delete(product);
        System.out.println("删除成功");
    }

    @Test
    public void saveAll(){
        List<Product> list = new ArrayList<Product>();
        for (int i = 0; i < 10; i++) {
            list.add(new Product(Long.valueOf(i), i+"华为手机", "手机", 1000d, "https://gimg2.baidu.com/image_search/"));
        }
        productDao.saveAll(list);
        System.out.println("批量保存成功");
    }

    @Test
    public void getByPageable(){
        Sort orders = Sort.by(Sort.Direction.DESC,"id");
        PageRequest pageRequest = PageRequest.of(0,5,orders);
        Page<Product> all = productDao.findAll(pageRequest);
        all.forEach(System.out::println);
    }

}
```

