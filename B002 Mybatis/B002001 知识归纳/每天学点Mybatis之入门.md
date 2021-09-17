[TOC]

# 1.简介

## 1.1 什么是Mybatis？

持久层框架，半自动映射（ORM）

-   支持自定义SQL、存储过程、高级映射。

-   免除几乎所有的JDBC代码、设置参数、获取结果集的工作
-   简单的 XML 或注解配置
-   映射原始类型、接口和POJO为数据库中的记录



## 1.2 构建流程

### 1.2.1 添加依赖包

```xml
<properties>
    <log4j.version>2.14.1</log4j.version>
    <slf4j.version>1.7.31</slf4j.version>
    <lombok.version>1.18.16</lombok.version>
    <jdbc-mysql>8.0.26</jdbc-mysql>
    <mybatis.version>3.5.7</mybatis.version>
</properties>

<dependencies>
    <!-- 单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
        <version>${log4j.version}</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>${log4j.version}</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-slf4j-impl</artifactId>
        <version>${log4j.version}</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>${slf4j.version}</version>
    </dependency>
    <!-- lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
    </dependency>
    <!-- MySQL 驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${jdbc-mysql}</version>
    </dependency>
    <!-- mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>${mybatis.version}</version>
    </dependency>
</dependencies>
```

### 1.2.2 构建 SqlSessionFactory

SqlSessionFactoryBuilder：通过加载 XML 配置文件或 Configuration 实例构建 SqlSessionFactory

#### 方式一：通过 XML 配置文件

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```



#### 方式二：通过 Configuration 实例

```java
// 数据源
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
// 事务
TransactionFactory transactionFactory = new JdbcTransactionFactory();
// 环境配置
Environment environment = new Environment("development", transactionFactory, dataSource);
// 配置
Configuration configuration = new Configuration(environment);
// DAO 映射
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

>   注意：XML 相关配置参考 《每天学点Mybatis之Configuration配置》



### 1.2.3 生成 SqlSession

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
    // ...
}
```



### 1.2.4 获取 DAO 接口的代理对象

```java
// 方式一：通过全限定名(命名空间+方法名)
Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);

// 方式二：通过类名
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(101);
```

>   注：Mapper 映射参考 《每天学点Mybatis之Mapper映射》



## 1.3 作用域（Scope）和生命周期

`SqlSessionFactoryBuilder`：可以被实例化、使用和丢弃，不要一直保留着它，以保证所有的 XML 解析资源可以被释放给更重要的事情，**最佳作用域是方法作用域**（也就是局部方法变量）。

`SqlSessionFactory`：避免重复创建，**最佳作用域是应用作用域**。最简单的就是使用单例模式或者静态单例模式。

`SqlSession`：每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的**最佳的作用域是请求或方法作用域**。绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。

```java
// 标准模式
try (SqlSession session = sqlSessionFactory.openSession()) {
  // 你的应用逻辑代码
}
```