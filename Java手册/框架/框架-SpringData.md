


#### 1. Repository接口详解

Spring Data可以让我们只定义接口，只要遵循 Spring Data的规范，就无需写实现类。 规范如下:

查询方法以 find | read | get 开头

涉及条件查询时，条件的属性用条件关键字连接

要注意的是：条件属性以首字母大写。

支持属性的级联查询. 若当前类有符合条件的属性, 则优先使用, 而不使用级联属性. ,若需要使用级联属性, 则属性之间使用 _ 进行连接.


**Repository 接口 是 Spring Data 的一个核心接口，它不提供任何方法，开发者需要在自己定义的接口中声明需要的方法**

```java
public interface Repository<T, ID extends Serializable> { }
```

#### 2. 继承体系：

Repository： 仅仅是一个标识，表明任何继承它的均为仓库接口类

CrudRepository：  继承 Repository，实现了一组 CRUD 相关的方法

PagingAndSortingRepository：  继承 CrudRepository，实现了一组分页排序相关的方法

JpaRepository： 继承 PagingAndSortingRepository，实现一组 JPA 规范相关的方法,自定义的 XxxxRepository 需要继承 JpaRepository，这样的 XxxxRepository 接口就具备了通用的数据访问控制层的能力。

JpaSpecificationExecutor：不属于Repository体系，实现一组 JPA Criteria 查询相关的方法

```java
@Test
public void contextLoads() {
	// 构造查询条件
	Specification<Person> specification = new Specification<Person>() {
		@Override
		public Predicate toPredicate(Root root, CriteriaQuery query, CriteriaBuilder builder) {
			List<Predicate> predicate = new ArrayList<>();
			Path<Integer> path = root.get("id");

			predicate.add(builder.equal(path, 1));

			Predicate[] pre = new Predicate[predicate.size()];
			query.where(predicate.toArray(pre));
			return query.getRestriction();
		}
	};

	Optional<Person> findOne = persionDao.findOne(specification);

	log.info("{}", findOne);

}

```

```java
@Test
public void queryTest(){

    /**
     * root:对象属性导航,用于根据属性名称定位属性
     * builder:拼装查询条件
     * query:执行查询
     */
    Specification<Fapi> specification= (root, query, builder) -> {
        Path<String> fid = root.get("fpartner");
        CriteriaQuery<Tuple> tupleQuery = builder.createTupleQuery();
        List<Predicate> predicates = new ArrayList<>();
        predicates.add(builder.equal(fid, "BD116D34F11CEB737CEC0EF16ADAFA71"));
        Predicate predicate[] = new Predicate[predicates.size()];
        tupleQuery.where(predicates.toArray(predicate));
        return tupleQuery.getRestriction();
    };

    Fapi fapi = fapiDao.findOne(specification);

    log.info("{}",fapi.getFsecret());

}
```



#### 3. Maven依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>springdata-demo-1</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>springdata-demo-1</name>
	<description>Demo project for Spring Boot</description>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.RELEASE</version>
		<relativePath />
	</parent>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>
	<dependencies>
		<!-- 唯一和springdata相关的依赖包,它会自动引入相关依赖 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<!-- MySQL驱动 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<!-- 阿里的连接池 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.29</version>
		</dependency>
		<!-- 日志,可以省略getset方法及控制台日志颜色配置 -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>
		<!-- SpringBoot热部署的依赖,可以没有 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>

```

#### 4. SpringBoot配置

application-dev.properties

```properties

server.port=8080
logging.level.org.springframework=info
logging.level.root=info

spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost/db_springdata?characterEncoding=utf-8&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

spring.jpa.hibernate.ddl-auto=update
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
spring.jpa.show-sql=true
```

application.properties

```properties
spring.profiles.active=dev
```

日志配置:logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<configuration>
    <!--定义一个带颜色的控制台输出日志PATTERN-->
    <property name="CONSOLE_LOG_PATTERN"
              value="时间:%date{yyyy-MM-dd HH:mm:ss} 级别: %blue(%-5level) 进程: %magenta(%thread) 类: %green(%logger) 信息: %msg%n"/>
    <!--定义一个不带颜色的文件日志输出格式,因为文件不能带颜色-->
    <property name="ERRINFOFILE_LOG_PATTERN"
              value="时间:%date{yyyy-MM-dd HH:mm:ss} 级别: %-5level 进程: %thread 类: %logger 信息: %msg%n"/>
    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                ${CONSOLE_LOG_PATTERN}
            </pattern>
        </layout>
    </appender>
    <root level="info">
        <appender-ref ref="consoleLog" />
    </root>
</configuration>
```
#### 5. 实体类

```java
package com.example.demo.dao;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;

import com.example.demo.domain.Person;

public interface PersionDao extends JpaRepository<Person, Integer>, JpaSpecificationExecutor<Person> {
	//根据 lastName 来获取对应的 Person
	Person getByLastName(String lastName);
}

```

#### 6. SpringBoot启动类

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.transaction.annotation.EnableTransactionManagement;
@SpringBootApplication(scanBasePackages = "com.example.demo")
@EnableTransactionManagement//开启事物
public class SpringdataDemo1Application {

	public static void main(String[] args) {
		SpringApplication.run(SpringdataDemo1Application.class, args);
	}
}

```

#### 7. 测试类
```java
package com.example.demo;

import java.util.Date;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import com.example.demo.dao.PersionDao;
import com.example.demo.domain.Person;

import lombok.extern.slf4j.Slf4j;

@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class SpringdataDemo1ApplicationTests {

	@Autowired
	private PersionDao persionDao;
	@Test
	public void insertTest(){
		Person person=new Person();
		person.setLastName("小黑");
		person.setBirth(new Date());
		person.setEmail("admin@coder163.com");
		persionDao.save(person);
	}
	@Test
	public void contextLoads() {
		log.info("{}",persionDao.getByLastName("小黑"));
	}

}

```

#### 8. 执行结果

``` properties
时间:2018-03-24 12:19:47 级别: INFO  进程: main 类: com.example.demo.SpringdataDemo1ApplicationTests 信息: Started SpringdataDemo1ApplicationTests in 2.524 seconds (JVM running for 3.189)
Hibernate: select next_val as id_val from hibernate_sequence for update
Hibernate: update hibernate_sequence set next_val= ? where next_val=?
Hibernate: insert into t_peson (birth, email, lastName, id) values (?, ?, ?, ?)
时间:2018-03-24 12:19:48 级别: INFO  进程: main 类: org.hibernate.hql.internal.QueryTranslatorFactoryInitiator 信息: HHH000397: Using ASTQueryTranslatorFactory
Hibernate: select person0_.id as id1_0_, person0_.birth as birth2_0_, person0_.email as email3_0_, person0_.lastName as lastName4_0_ from t_peson person0_ where person0_.lastName=?

```

***
[项目代码](https://github.com/coder163/demo)