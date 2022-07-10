> 一、主要内容 spring boot 引入 Elasticsearch ElasticsearchTemplate 的使用 ElasticsearchRepository 的使用 二、环境整合 创建 Elasticsearch 工程，引入依赖 一般情况下，都会单独创建一个工程，用于操作 es。

一、主要内容
------

*   spring boot 引入 Elasticsearch
*   ElasticsearchTemplate 的使用
*   ElasticsearchRepository 的使用

二、环境整合
------

### 创建 Elasticsearch 工程，引入依赖

一般情况下，都会单独创建一个工程，用于操作 es。

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-elasticsearch</artifactId>
	<version>2.5.1</version>
</dependency>


```

### 版本统一

目前`springboot-data-elasticsearch`最新版本是 2.5.1，这个版本使用的 es 版本为 7.12.1，需要注意的是，我们这里引入的版本需要与服务器安装的版本一致。

可以通过 Maven 仓库进行查看版本信息，地址：https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-elasticsearch

### 修改配置文件

```
spring: 
 data: 
  elasticsearch: 
   cluster-name: es-cluster
   cluster-nodes: 10.133.28.55:9300


```

三、ElasticsearchTemplate 的使用
---------------------------

ElasticsearchTemplate 类似于 RedisTemplate，是 spring 提供的较为底层的与 Elasticserch 交互的类。

### 常用注解

##### @Document

注解作用在类上，标记实体类为文档对象，指定实体类与索引对应关系。常用配置项有：

*   indexName：索引名称
*   type: 索引类型，默认为空
*   shards: 主分片数量，默认 5
*   replicas：复制分片数量，默认 1
*   createIndex：创建索引，默认为 true

##### @Id

指定文档 ID，加上这个注解，文档的`_id`会与我们的数据 ID 是一致的，否则在不给定默认值的情况下，es 会自动创建。

##### @Field

指定普通属性，标明这个是文档中的一个字段。常用的配置项有：

*   type： 对应 Elasticsearch 中属性类型。默认自动检测。使用 FiledType 枚举可以快速获取

```
public enum FieldType {
	Text,
	Integer,
	Long,
	Date,
	Float,
	Double,
	Boolean,
	Object,
	Auto,
	Nested,
	Ip,
	Attachment,
	Keyword
}


```

*   index： 是否创建倒排索引，一般不需要分词的属性不需要创建索引
    
*   analyzer：指定索引类型。
    
*   store：是否进行存储，默认不进行存储。
    
    > 其实不管我们将 store 值设置为 true 或 false，elasticsearch 都会将该字段存储到 Field 域中；但是他们的区别是什么？
    > 
    > 1.  store = false 时，默认设置；那么给字段只存储在 "_source" 的 Field 域中；
    >     
    > 2.  store = true 时，该字段的 value 会存储在一个跟_source 平级的独立 Field 域中；同时也会存储在_source 中，所以有两份拷贝。
    >     
    > 
    > 那么我们在什么样的业务场景下使用 store field 功能？
    > 
    > 1.  _source field 在索引的 mapping 中 disable 了。这种情况下，如果不将某个 field 定义成 store=true，那些将无法在返回的查询结果中看到这个 field。
    > 2.  _source 的内容非常大。这时候如果我们想要在返回的_source document 中解释出某个 field 的值的话，开销会很大（当然你也可以定义 source filtering 将减少 network overhead），比例某个 document 中保存的是一本书，所以 document 中可能有这些 field: title, date, content。假如我们只是想查询书的 title 跟 date 信息，而不需要解释整个_source（非常大），这个时候我们可以考虑将 title, date 这些 field 设置成 store=true。
    

### 创建实体

```
@Document(indexName = "item",type = "_doc",createIndex = "false")
public class Item {
    @Id
    private Long id;
    
    @Field(type = FieldType.Text,analyzer = "ik_max_word")
    private String title;
    
    @Field(type=FieldType.Keyword)
    private String brand;
    @Field(type=FieldType.Double)
    private Double price;
    
    @Field(index = false,type = FieldType.Keyword)
    private String images;
}


```

### 索引管理

ElasticsearchTemplate 提供了创建索引的方法，但是不建议使用 ElasticsearchTemplate 对索引进行管理（创建索引，更新映射，删除索引）。

索引就像是数据库或者数据库中的表，我们平时是不会是通过 java 代码频繁的去创建修改删除数据库或者表的相关信息，我们只会针对数据做 CRUD 的操作。

```
elasticsearchTemplate.createIndex(Class<T> clazz);

elasticsearchTemplate.deleteIndex(Class<T> clazz);


```

### 创建文档

##### 新增单条文档

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class ESTest {
    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;
    @Test
    public void insertItemDoc(){
        Item item = new Item(1001L,"XXX1","XXX1","XXX1");
        IndexQuery indexQuery = new IndexQueryBuilder().withObject(item).build();
        elasticsearchTemplate.index(indexQuery);
    }
}


```

##### 批量新增文档

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class ESTest {
    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;
    @Test
    public void insertItemDocBulk() {
    	List<Item> list = new ArrayList<>();
        list.add(new IndexQueryBuilder().withObject(new Item(1001L,"XXX1","XXX1","XXX1")).build())
        list.add(new IndexQueryBuilder().withObject(new Item(1002L,"XXX2","XXX2","XXX2")).build())
        IndexQuery indexQuery = new IndexQueryBuilder().withObject(item).build();
        elasticsearchTemplate.index(indexQuery);
    }
}


```

### 删除文档

删除都是根据主键进行删除，提供了两个方法：

*   delete(String indexName,String typeName,String id); 通过字符串指定索引，类型和 id 值；
*   delete(Class,String id) 第一个参数传递实体类类类型，建议使用此方法，减少索引名和类型名由于手动编写出现错误的概率。

代码比较简单，不再示例。

### 修改文档

```
@Test
public void updateItemDoc() {
    Map<String, Object> sourceMap = new HashMap<>();
    sourceMap.put("title", "YYY");

    IndexRequest indexRequest = new IndexRequest();
    indexRequest.source(sourceMap);

    UpdateQuery updateQuery = new UpdateQueryBuilder()
        .withClass(Stu.class)
        .withId("1001")
        .withIndexRequest(indexRequest)
        .build();
    elasticsearchTemplate.update(updateQuery);
}


```

### 查询文档

##### 模糊查询

使用查询条件与所有的 field 进行匹配，进行查询

> 对于一些入参是不是看着很陌生？感觉头大是不是，别急，最后有解释。

```
@Test
public void queryItemDoc() {
    
    QueryStringQueryBuilder queryStringQueryBuilder = QueryBuilders.queryStringQuery("少年");
    
    SearchQuery searchQuery = new NativeSearchQuery(queryStringQueryBuilder);
    List<Item> list = elasticsearchTemplate.queryForList(searchQuery, Item.class);
    for(Item item : list){
        System.out.println(item);
    }
}


```

##### 使用 match_all 查询所有文档

```
@Test
public void matchAllItemDoc() {
   SearchQuery searchQuery = new NativeSearchQuery(QueryBuilders.matchAllQuery());
    List<Item> list = elasticsearchTemplate.queryForList(searchQuery, Item.class);
    for(Item item : list){
        System.out.println(item);
    }
}


```

##### 使用 match 查询分页文档并排序

```
@Test
public void matchItemDoc() {
    
   	Pageable pageable = PageRequest.of(0, 2);
    
    SortBuilder sortBuilder = new FieldSortBuilder("price")
                .order(SortOrder.DESC);
    SearchQuery query = new NativeSearchQueryBuilder()
        .withQuery(QueryBuilders.matchQuery("title", "english"))
        .withPageable(pageable)
        .withSort(sortBuilder)
        .build();
    AggregatedPage<Item> pagedItem = elasticsearchTemplate.queryForPage(query, Item.class);
    System.out.println("查询后的总分页数目为：" + pagedItem.getTotalPages());
    List<Item> list = pagedItem.getContent();
    for (Item item : list) {
        System.out.println(item);
    }
}


```

##### 多条件查询

```
 @Test
 public void mustShouldItemDoc(){
     BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
     List<QueryBuilder> listQuery = new ArrayList<>();
     listQuery.add(QueryBuilders.matchPhraseQuery("title","XX"));
     listQuery.add(QueryBuilders.rangeQuery("price").gte(10).lte(100));
     
     boolQueryBuilder.must().addAll(listQuery);  
     SearchQuery searchQuery = new NativeSearchQuery(boolQueryBuilder);
     List<Item> list = elasticsearchTemplate.queryForList(searchQuery, Item.class);
     for(Item item : list){
         System.out.println(item);
     }
 }


```

##### 高亮查询

```
@Test
public void highlightStuDoc(){
    
    String preTag = "<font color='red'>";
    String postTag = "</font>";

    Pageable pageable = PageRequest.of(0, 10);

    SortBuilder sortBuilder = new FieldSortBuilder("price")
        .order(SortOrder.DESC);
	
    SearchQuery query = new NativeSearchQueryBuilder()
        .withQuery(QueryBuilders.matchQuery("title", "XXX"))
        .withHighlightFields(new HighlightBuilder.Field("title")
                             .preTags(preTag)
                             .postTags(postTag))
        .withSort(sortBuilder)
        .withPageable(pageable)
        .build();
    AggregatedPage<Item> paged = esTemplate.queryForPage(query, Item.class, new SearchResultMapper() {
        @Override
        public <T> AggregatedPage<T> mapResults(SearchResponse response, Class<T> clazz, Pageable pageable) {
			
            List<Item> listHighlight = new ArrayList<>();
			
            SearchHits hits = response.getHits();
            for (SearchHit h : hits) {
                
                HighlightField highlightField = h.getHighlightFields().get("title");
                String title = highlightField.getFragments()[0].toString();
				
                Object id = (Object)h.getSourceAsMap().get("id");
                String brand = (String)h.getSourceAsMap().get("brand");
                Object price = (Object)h.getSourceAsMap().get("price");
                String images = (String)h.getSourceAsMap().get("images");
				
                Item itemHL = new Item();
                item.setId(Long.valueOf(id.toString()));
                item.setTitle(title);
                item.setBrand(brand);
                item.setPrice(Double.valueof(price.toString));
                item.setImages(images);

                listHighlight.add(itemHL);
            }

            if (listHighlight.size() > 0) {
                return new AggregatedPageImpl<>((List<T>)listHighlight);
            }
            return null;
        }
    });
    System.out.println("查询后的总分页数目为：" + paged.getTotalPages());
    List<Item> list = paged.getContent();
    for (Item item : list) {
        System.out.println(item);
    }
}


```

四、ElasticsearchRepository 的使用
-----------------------------

ElasticsearchRepository 是基于 ElasticsearchTemplate 封装的一个接口用于操作 Elasticsearch。

它主要干了啥呢？我们能通过它干些什么呢？

*   它主要帮我们封装了一些常用的方法。如下图：
    
    ![](https://img-blog.csdnimg.cn/img_convert/a1f4bcf3f7c9eab016dcf2a2ce3c9aba.png)
    
*   我们还可以通过使用`Not` `Add` `Like` `Or` `Between`等关键词自动创建查询语句。它会自动帮你完成，无需写实现类。
    
    比如：你的方法名叫做：findByTitle，那么它就知道你是根据 title 查询，然后自动帮你完成，无需写实现类。
    
    【自定义方法命名约定】：
    
    ![](https://img-blog.csdnimg.cn/img_convert/5058e41708b2529fb3a27a56b0dce945.png)
    

### 自定义 Repository 接口

这个是遵循 SpringData 的规范，在自定义接口中直接指定查询方法名称便可查询，无需进行实现。在 idea 里面也会提示 ES 里面有的字段，写起来挺方便的。

```
public interface ItemRepository extends ElasticsearchRepository<Item,Long> {
    
    List<Item> findByPriceBetween(double price1, double price2);
}


```

### 创建文档

##### 新增单条文档

可以直接调用`ItemRepository`的`save()`方法。

```
@Autowired
private ItemRepository itemRepository;
@Test
public void insert(){
    Item item = new Item(1L, "小米手机7", "小米", 3499.00, "http://xxx/xxx.jpg");
    itemRepository.save(item);
}


```

##### 批量新增文档

插入多条文档数据使用的`saveAll()`方法

```
@Autowired
private ItemRepository itemRepository;
@Test
public void indexList() {
    List<Item> list = new ArrayList<>();
    list.add(new Item(1L, "小米手机7", "小米", 3299.00, "http://xxx/1.jpg"));
    list.add(new Item(2L, "坚果手机R1", "锤子", 3699.00, "http://xxx/1.jpg"));
    list.add(new Item(3L, "华为META10", "华为", 4499.00, "http://xxx/1.jpg"));
    list.add(new Item(4L, "小米Mix2S", "小米", 4299.00, "http://xxx/1.jpg"));
    list.add(new Item(5L, "荣耀V10", "华为", 2799.00, "http://xxx/1.jpg"));
    
    itemRepository.saveAll(list);
}


```

### 修改文档

修改文档数据也是使用的`save()`方法。

### 查询参数介绍

我们通过上面的方法截图，可以看出来`ElasticsearchRepository`提供了一些特殊的`search()`方法，用来构建一些查询。这几个方法里面的参数主要是`SearchQuery`和`QueryBuilder`，所以要完成一些特殊查询主要就是构建这两个参数。

**SearchQuery**

通过源码我们可以看到，`SearchQuery`是一个接口，有一个实现类叫`NativeSearchQuery`，实际使用中，我们的主要任务就是构建`NativeSearchQuery`来完成一些复杂的查询的。

![](https://img-blog.csdnimg.cn/img_convert/d5415a12e6fb8a999f148381fa7947d5.png)

我们可以看到`NativeSearchQuery`的主要入参是`QueryBuilder`、`SortBuilder`、`HighlightBuilder`

一般情况下，我们不是直接是`new NativeSearchQuery`，而是使用`NativeSearchQueryBuilder`来完成 NativeSearchQuery 的构建。

``` java
NativeSearchQueryBuilder		
    .withQuery(QueryBuilder1)
    .withFilter(QueryBuilder2)
    .withSort(SortBuilder1)
    .withXXXX()
    .build();

//例子
SearchQuery searchQuery = new NativeSearchQueryBuilder()
        .withQuery(QueryBuilders.multiMatchQuery("互联网寒冬", "title", "content"))  //查询词与要匹配的数据库属性
        .withSort(SortBuilders.fieldSort("type").order(SortOrder.DESC)) //根据type排序
        .withSort(SortBuilders.fieldSort("score").order(SortOrder.DESC))
        .withSort(SortBuilders.fieldSort("createTime").order(SortOrder.DESC))
        .withPageable(PageRequest.of(0, 10))  //设置分页
        .withHighlightFields(
                new HighlightBuilder.Field("title").preTags("<em>").postTags("</em>"),
                new HighlightBuilder.Field("content").preTags("<em>").postTags("</em>")   //用<em> </em>标签包裹查询词，可以进行css样式渲染。
        ).build();


```

**QueryBuilder**

QueryBuilder 是一个接口，它有非常多的实现类，可以用于不同的查询条件构建。

要构建 QueryBuilder，我们可以使用工具类 QueryBuilders，里面有大量的方法用来完成各种各样的 QueryBuilder 的构建，字符串的、Boolean 型的、match 的、地理范围的等等。

```
SearchQuery searchQuery = new NativeSearchQueryBuilder()
    .withQuery(QueryBuilders.queryStringQuery("spring boot OR 书籍"))
    .build();


```

查询方法跟上面介绍的`ElasticsearchTemplate`差不多，就不再单独举例了。

综上，我们在一般的场景下使用`ElasticsearchRepository`就可以满足我们的需要，在一些复杂的查询场景下，可以配合`ElasticsearchTemplate`来使用。

最后，欢迎关注「Hugh 的白板」公号，私信我一起学习，一起成长！

最最后，如果对你有一点帮助，可否给一个赞？非常感谢哦~~~

本内容为合法授权发布，文章内容为作者独立观点，不代表开发云立场，未经允许不得转载。
