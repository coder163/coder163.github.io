### 1. 什么是全文检索

**全文检索是一种将文件中所有文本与检索项匹配的文字资料检索方法。**

全文检索首先将要搜索的目标文档中的词提取出来，组成索引，通过查询索引达到搜索目标文档的目的。

**这种先建立索引，再对索引进行搜索的过程就叫全文检索（Full-text Search）。**

### 2. Lucene概述

Lucene是apache下的一个开放源代码的**全文检索引擎工具包**。提供了完整的查询引擎和索引引擎，部分文本分析引擎。Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能。

#### 2.1. Lucene与搜索引擎的区别

全文检索系统是按照全文检索理论建立起来的用于提供全文检索服务的软件系统。全文检索系统是一个可以运行的系统，包括建立索引、处理查询返回结果集、增加索引、优化索引结构等功能。例如：百度搜索、eclipse帮助搜索、淘宝网商品搜索。

搜索引擎是全文检索技术最主要的一个应用，例如百度。搜索引擎起源于传统的信息全文检索理论，即计算机程序通过扫描每一篇文章中的每一个词，建立以词为单位的倒排文件，检索程序根据检索词在每一篇文章中出现的频率和每一个检索词在一篇文章中出现的概率，对包含这些检索词的文章进行
排序，最后输出排序的结果。全文检索技术是搜索引擎的核心支撑技术。

Lucene和搜索引擎不同，**Lucene是一套用java或其它语言写的全文检索的工具包**，为应用程序提供了很多个api接口去调用，可以简单理解为是一套实现全文检索的类库，**搜索引擎是一个全文检索系统，它是一个单独运行的软件。**

#### 2.2. Lucene主要包结构



|             包名              |                                        功能                                         |
| ----------------------------- | ----------------------------------------------------------------------------------- |
| org.apache.lucene.analysis    | 语言分析器，主要用于的切词Lucene提供的分析器实现类在：lucene-analyzers-common-4.10.3.jar |
| org.apache.lucene.document    | 索引存储时的文档结构管理，类似于关系型数据库的表结构                                     |
| org.apache.lucene.index       | 索引管理，包括索引建立、删除等                                                         |
| org.apache.lucene.queryParser | 查询分析器，实现查询关键词间的运算，如与、或、非等, 生成查询表达式，                       |
| org.apache.lucene.search      | 检索管理，根据查询条件，检索得到结果                                                    |
| org.apache.lucene.store       | 数据存储管理，包括一些I/O操作                                                          |
| org.apache.lucene.util        | 公用类                                                                              |





### 3. 概念-文档域

对非结构化的数据统一格式为document文档格式，一个文档有多个field域，不同的文档其field的个数可以不同，建议相同类型的文档包括相同的field。

文档域作用：将非结构化数据进行统一结构化，将document中field中的value值进行分词。


### 4. 概念-索引域

用于搜索，搜索程序将从索引域中搜索一个一个词，根据词找到对应的文档。

将Document中的Field的内容进行分词，将分好的词创建索引，索引=Field域名:词


### 5. 概念-倒排索引表

传统方法是已知文件在文件中找内容，在文件内容中匹配搜索关键字，这种方法是顺序扫描方法，数据量大就搜索慢。


倒排索引结构是根据关键字（词语）找文档，倒排索引结构也叫反向索引结构，包括索引和文档两部分，索引即词汇表，它是在索引中匹配搜索关键字，由于索引内容量有限并且采用固定优化算法搜索速度很快，找到了索引中的词汇，词汇与文档关联，从而最终找到了文档。


### 6. Field属性

Field是文档中的域，包括Field名和Field值两部分，一个文档可以包括多个Field，Document只是Field的一个承载体，Field值即为要索引的内容，也是要搜索的内容。

#### 6.1. 是否分词(tokenized)

是：将Field值分词其目的是将词进行索引，比如：图书名称、图书简介等，这些内容用户要输入关键字搜索，由于搜索的内容格式大、内容多需要分析后将语汇单元索引。

否：不作分词处理，比如：图书id、订单号、身份证号等


#### 6.2. 是否索引(indexed)

是：索引的目的是为了搜索，可以将Field分词后进行索引也可以对整个Field进行索引。比如：图书名称、图书简介分词后进行索引，订单号、身份证号不用分词但也要索引，这些将来都要作为查询条件，如果将来查询要根据图书表的主键id查询，那图书id也要索引

否：不索引无法搜索到，比如：图片路径等，不用作为查询条件的不用索引。


#### 6.3. 是否存储(stored)

是：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取，比如：图书名称、订单号，凡是将来要从Document中获取的Field都要存储。

否：不存储Field值，不存储的Field无法通过Document获取，比如：图书简介，内容较大不用存储。如果要向用户展示商品简介可以从系统的关系数据库中获取商品简介，思路是从lucene索引获取图书id，根据图书id查询关系数据库。


#### 6.4. Field常用类型



|                                 Field类                                  |       数据类型       | 是否分词 | 是否索引 | 是否存储 |                                                             说明                                                              |
| ------------------------------------------------------------------------ | -------------------- | ------- | ------- | ------- | ---------------------------------------------------------------------------------------------------------------------------- |
| StringField(FieldName, FieldValue,Store.YES))                            | 字符串               | N       | Y       | Y或N    | 这个Field用来构建一个字符串Field，但是不会进行分析，会将整个串存储在索引中，比如(订单号,姓名等)是否存储在文档中用Store.YES或Store.NO决定 |
| LongField(FieldName, FieldValue,Store.YES)                               | Long型               | Y       | Y       | Y或N    | 这个Field用来构建一个Long数字型Field，进行分析和索引，比如(价格)是否存储在文档中用Store.YES或Store.NO决定                            |
| StoredField(FieldName, FieldValue)                                       | 重载方法，支持多种类型 | N       | N       | Y       | 这个Field用来构建不同类型Field不分析，不索引，但要Field存储在文档中                                                                |
| TextField(FieldName, FieldValue, Store.NO)或TextField(FieldName, reader) | 字符串或流            | Y       | Y       | Y或N    | 如果是一个Reader, lucene猜测内容比较多,会采用Unstored的策略.                                                                     |


### 7. 分词器

将一句话按照指定的规则进行拆分不同的词语，而这个规则就是分词器来实现

在Lucene的lucene-analyzers-common包中提供了很多分析器，比如：org.apache.lucene.analysis.standard.standardAnalyzer标准分词器，它是Lucene的核心分词器，它对分析文本进行分词、大写转成小写、去除停用词、去除标点符号等操作过程。

**停用词**是为节省存储空间和提高搜索效率，搜索引擎在索引页面或处理搜索请求时会自动忽略某些字或词，这些字或词即被称为Stop Words(停用词)。比如语气助词、副词、介词、连接词等，通常自身并无明确的意义，只有将其放入一个完整的句子中才有一定作用，如常见的“的”、“在”、“是”、“啊”等。


#### 7.1. 中文分词器

英文是以单词为单位的，单词与单词之间以空格或者逗号句号隔开。而中文则以字为单位，字又组成词，字和词再组成句子。所以对于英文，我们可以简单以空格判断某个字符串是否为一个单词，比如I love China，love 和 China很容易被程序区分开来；但中文“我爱中国”就不一样了，电脑不知道“中国”是一个词语还是“爱中”是一个词语。把中文的句子切分成有意义的词，就是中文分词，也称切词。我爱中国，分词的结果是：我 爱 中国。

[百度打击谷歌中文分词广告](http://video.baomihua.com/v/16156927?from=video)



### 8. solr概述

Solr 是Apache下的一个顶级开源项目，采用Java开发，它是基于Lucene的全文搜索服务器。Solr提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展，并对索引、搜索性能进行了优化

Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr 索引的实现方法很简单，用 POST 方法向 Solr 服务器发送一个描述 Field 及其内容的 XML 文档，Solr根据xml文档添加、删除、更新索引 。Solr 搜索只需要发送 HTTP GET 请求，然后对 Solr 返回Xml、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况

### 9. Solr安装及配置

从Solr官方网站（http://lucene.apache.org/solr/ ）下载Solr4.10.3，根据Solr的运行环境，Linux下需要下载lucene-4.10.3.tgz，windows下需要下载lucene-4.10.3.zip。

Solr使用指南可参考：https://wiki.apache.org/solr/FrontPage。

#### 9.1. Solr整合tomcat

创建一个Solr home目录，SolrHome是Solr运行的主目录，目录中包括了运行Solr实例所有的配置文件和数据文件，Solr实例就是SolrCore，一个SolrHome可以包括多个SolrCore（Solr实例），每个SolrCore提供单独的搜索和索引服务。

#### 9.2. 整合步骤

solr本身是一个web项目，所以理论上复制到Tomcat的webapps下即可

Tomcat默认使用日志编码是utf-8，在控制台会乱码，那么需要logging.properties文件中的编码设置，改为GBK

**解压solr，war包的选择(二选一)**

- solr-4.10.3\dist\solr-4.10.3.war
- \solr-4.10.3\example\webapps\solr.war

**添加依赖**

- 默认情况下，war中缺少日志依赖，所以我们需要将依赖添加一下：solr-4.10.3\example\lib\ext\目下所有的jar

此时启动会报错

- 错误提示：{msg=SolrCore 'collection1' is not available due to init failure: Could not load conf for core collection1
- 大概的含义是因为我们没有配置solr-home目录导致solr无法找到对应实例



**配置solr实例(collection1)**

- 修改Tomcat下的solr项目中web.xml,让它指向一个固定的目录

```xml
<env-entry>
	<env-entry-name>solr/home</env-entry-name>
	<env-entry-value>E:\solr-home</env-entry-value>
	<env-entry-type>java.lang.String</env-entry-type>
</env-entry>
```

**将\solr-4.10.3\example\solr\目录下所有的文件及文件夹复制到对应solr-home目录中**

> 启动Tomcat即可，访问：http://localhost:8080/solr


### 10. schema.xml(核心配置)

在solr中所有的CRUD操作的字段必须**先进行定义，然后在使用**，而字段定义规则如下

#### 10.1. 定义普通字段

```xml
<field name="_version_" type="long" indexed="true" stored="true"/>
```

name:是指solr中的域的类型，注意不是java类型，这些数据类型决定该字段是否可以分词、索引、存储

indexed: 是否对该字段建立索引，一旦建立索引我们查询时，可以根据该索引进行查询

stored：该字段在索引库是否存储，如果存储，那么将来会在查询结果中显示


```xml
<field name="id" type="string" indexed="true" stored="true" required="true" multiValued="false" />
```

required：必须的，当我们添加记录的时候，该字段不能为空

multiValued：是否允许有多个值


#### 10.2. 动态字段(dynamicField)

主要作用是：模糊匹配

```xml
<dynamicField name="*_i"  type="int"    indexed="true"  stored="true"/>
```

#### 10.3. 复制字段(copyField)

将某多个字段关联同一个字段上，从而简化搜索

```xml
<copyField source="cat" dest="text"/>
```


source：被关联的字段

dest:关联的字段


#### 10.4. 唯一字段(uniqueKey)

该字段要求全局唯一，例如：id，当我们执行update操作时，该ID的值如果存在则删除原来的值重新添加现在的值


#### 10.5. 字段类型(域类型)

```xml
<fieldType name="string" class="solr.StrField" sortMissingLast="true" />
```

name：类型的名称

class：该类型实际对应的Java类型，在solr中定义了很多字段类型，有的可以分词、有的不可以分词，为了区分不同字段所以定义类型



### 11. 配置中文分词器

1. 把IKAnalyzer2012FF_u1.jar添加到solr/WEB-INF/lib目录下。

2. 复制IKAnalyzer的配置文件和自定义词典和停用词词典到solr的classpath下，也就是在apache-tomcat-7.0.54\webapps\solr\WEB-INF目录下新建classes目录,将配置文件和词典放进去.

3. 在schema.xml中添加一个自定义的fieldType，使用中文分析器。

```xml
<!-- IKAnalyzer-->
<fieldType name="text_ik" class="solr.TextField">
	<analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>

<!--IKAnalyzer Field-->
<field name="title_ik" type="text_ik" indexed="true" stored="true" />
<field name="content_ik" type="text_ik" indexed="true" stored="false" multiValued="true"/>
```

### 12. 集成数据导入插件

1. 把dataimport插件依赖和**mysql的数据库驱动**的jar包添加到solrcore（collection1\lib）中

2. 配置solrconfig.mxl文件，添加一个requestHandler。

```xml
<requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
	<lst name="defaults">
	<str name="config">data-config.xml</str>
	</lst>
</requestHandler>
```

3. 创建一个data-config.xml，保存到collection1\conf\目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<dataConfig>
	<dataSource type="JdbcDataSource"
			driver="com.mysql.jdbc.Driver"
			url="jdbc:mysql://localhost:3306/lucene"
			user="root"
			password="root"/>
	<document>
		<entity name="product" query="SELECT pid,name,catalog_name,price,description,picture FROM products ">
			<field column="pid" name="id"/>
			<field column="name" name="product_name"/>
			<field column="catalog_name" name="product_catalog_name"/>
			<field column="price" name="product_price"/>
			<field column="description" name="product_description"/>
			<field column="picture" name="product_picture"/>
		</entity>
	</document>
</dataConfig>
```


### 13. Java操作Solr

solrj是访问Solr服务的java客户端，提供索引和搜索的请求方法，SolrJ通常在嵌入在业务系统中，通过SolrJ的API接口操作Solr服务

在非SpringBoot环境下

```java
//向索引库中添加索引
@Test
public void addDocument() throws Exception {
	//和solr服务器创建连接
	//参数：solr服务器的地址
	SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
	//创建一个文档对象
	SolrInputDocument document = new SolrInputDocument();
	//向文档中添加域
	//第一个参数：域的名称，域的名称必须是在schema.xml中定义的
	//第二个参数：域的值
	document.addField("id", "c0001");
	document.addField("title_ik", "使用solrJ添加的文档");
	document.addField("content_ik", "文档的内容");
	document.addField("product_name", "商品名称");
	//把document对象添加到索引库中
	solrServer.add(document);
	//提交修改
	solrServer.commit();

}

//删除文档，根据id删除
@Test
public void deleteDocumentByid() throws Exception {
	//创建连接
	SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
	//根据id删除文档
	solrServer.deleteById("c0001");
	//提交修改
	solrServer.commit();
}

//根据查询条件删除文档
@Test
public void deleteDocumentByQuery() throws Exception {
	//创建连接
	SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
	//根据查询条件删除文档
	solrServer.deleteByQuery("*:*");
	//提交修改
	solrServer.commit();
}
//查询索引
@Test
public void queryIndex() throws Exception {
	//创建连接
	SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
	//创建一个query对象
	SolrQuery query = new SolrQuery();
	//设置查询条件
	query.setQuery("*:*");
	//执行查询
	QueryResponse queryResponse = solrServer.query(query);
	//取查询结果
	SolrDocumentList solrDocumentList = queryResponse.getResults();
	//共查询到商品数量
	System.out.println("共查询到商品数量:" + solrDocumentList.getNumFound());
	//遍历查询的结果
	for (SolrDocument solrDocument : solrDocumentList) {
		System.out.println(solrDocument.get("id"));
		System.out.println(solrDocument.get("product_name"));
		System.out.println(solrDocument.get("product_price"));
		System.out.println(solrDocument.get("product_catalog_name"));
		System.out.println(solrDocument.get("product_picture"));

	}
}

//复杂查询索引
@Test
public void queryIndex2() throws Exception {
	//创建连接
	SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
	//创建一个query对象
	SolrQuery query = new SolrQuery();
	//设置查询条件
	query.setQuery("钻石");
	//过滤条件
	query.setFilterQueries("product_catalog_name:幽默杂货");
	//排序条件
	query.setSort("product_price", ORDER.asc);
	//分页处理
	query.setStart(0);
	query.setRows(10);
	//结果中域的列表
	query.setFields("id","product_name","product_price","product_catalog_name","product_picture");
	//设置默认搜索域
	query.set("df", "product_keywords");
	//高亮显示
	query.setHighlight(true);
	//高亮显示的域
	query.addHighlightField("product_name");
	//高亮显示的前缀
	query.setHighlightSimplePre("<em>");
	//高亮显示的后缀
	query.setHighlightSimplePost("</em>");
	//执行查询
	QueryResponse queryResponse = solrServer.query(query);
	//取查询结果
	SolrDocumentList solrDocumentList = queryResponse.getResults();
	//共查询到商品数量
	System.out.println("共查询到商品数量:" + solrDocumentList.getNumFound());
	//遍历查询的结果
	for (SolrDocument solrDocument : solrDocumentList) {
		System.out.println(solrDocument.get("id"));
		//取高亮显示
		String productName = "";
		Map<String, Map<String, List<String>>> highlighting = queryResponse.getHighlighting();
		List<String> list = highlighting.get(solrDocument.get("id")).get("product_name");
		//判断是否有高亮内容
		if (null != list) {
			productName = list.get(0);
		} else {
			productName = (String) solrDocument.get("product_name");
		}

		System.out.println(productName);
		System.out.println(solrDocument.get("product_price"));
		System.out.println(solrDocument.get("product_catalog_name"));
		System.out.println(solrDocument.get("product_picture"));

	}
}
```
#### 13.1. Springboot环境

依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-solr</artifactId>
</dependency>
```

启动类

```java
@SpringBootApplication
public class SpringBootSolrApplication {

    @Bean
    public SolrTemplate solrTemplate(SolrClient client){

       return new SolrTemplate(client);

    }
    public static void main(String[] args) {
        SpringApplication.run(SpringBootSolrApplication.class, args);
    }

}
```

实体类

```java

@Data
public class Product {
    @Field
    private int id;
    @Field("product_img")
    private String img;
    @Field("product_color")
    private String color;
    @Field("product_name")
    private String name;
    @Field("product_title")
    private String title;

}
```

单元测试

```java

@SpringBootTest
public class SpringBootSolrApplicationTests {
  

    @Autowired
    private SolrTemplate template;
    //根据ID查询数据
    @Test
    public void contextLoads() {

        Optional<Product> template = this.template.getById("collection1", "370", Product.class);

        System.out.println(template.get());

    }

    //添加一条记录
    @Test
    public void add() {


        Product product = new Product();
        product.setId(1111);
        product.setName("aaaa");
        product.setTitle("ccc");
        UpdateResponse bean = this.template.saveBean("collection1", product);
        //添加操作必须要提交
        this.template.commit("collection1");

    }

    //根据ID删除一条记录
    @Test
    public void deleteDemo(){
        UpdateResponse delete = this.template.deleteByIds("collection1", "1111");
        this.template.commit("collection1");
    }

    //根据条件删除
    @Test
    public void updateDemo(){

        SimpleQuery query=new SimpleQuery("id:370");
//        SimpleQuery query=new SimpleQuery("*:*");

        this.template.delete("collection1",query,Product.class);

        this.template.commit("collection1");
    }

    //根据条件查询
    @Test
    public void simpleQueryDemo(){
        SimpleQuery query=new SimpleQuery("id:372");
        Page<Product> page = this.template.query("collection1", query, Product.class);
        //查询的内容
        List<Product> content = page.getContent();

        System.out.println(content);

    }
    //分页
    @Test
    public void simpleQueryPageDemo(){
        SimpleQuery query=new SimpleQuery("*:*");

        SolrPageRequest request=new SolrPageRequest(0,2);

        query.setPageRequest(request);
        Page<Product> page = this.template.query("collection1", query, Product.class);
        //查询的内容
        List<Product> content = page.getContent();

        System.out.println(content);


    }

    //动态拼装过滤条件
    @Test
    public void queryByCriteriaDemo(){
        SimpleQuery query=new SimpleQuery("*:*");
        //拼装过滤条件
        Criteria criteria = new Criteria("color").contains("蓝");
        query.addCriteria(criteria);

        Page<Product> page = this.template.query("collection1", query, Product.class);
        //查询的内容
        List<Product> content = page.getContent();

        System.out.println(content);
    }

    //高亮
    @Test
    public void queryByHi(){

        HighlightQuery query=new SimpleHighlightQuery();

        //高亮的选项
        HighlightOptions options=new HighlightOptions();

        options.setSimplePrefix("<span style='color:red'>");

        options.addField("product_title");

        options.setSimplePostfix("</span>");

        query.setHighlightOptions(options);

        Criteria criteria = new Criteria("product_title").contains("小米");
        query.addCriteria(criteria);

        HighlightPage<Product> page = this.template.queryForHighlightPage("collection1", query, Product.class);

        List<HighlightEntry<Product>> highlighted = page.getHighlighted();

        for (HighlightEntry<Product> entry : highlighted) {
            Product entity = entry.getEntity();
            /*
            List<HighlightEntry.Highlight> highlights = entry.getHighlights();

            for (HighlightEntry.Highlight highlight:highlights){
                System.out.println( highlight.getSnipplets().get(0));
            }*/
            List<HighlightEntry.Highlight> highlights = entry.getHighlights();

            if (highlights.size() > 0 ) {

                String s = highlights.get(0).getSnipplets().get(0);
                entity.setTitle(s);
            }
        }
    }

}

```