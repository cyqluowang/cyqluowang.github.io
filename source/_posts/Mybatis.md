title: Mybatis
date: 2017-02-22 17:47:16
tags: spring
---
# Spring集成Mybatis
1、添加依赖
```
<dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
       <version>1.2.0</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

mybatis-spring-boot-starter 快速集成mybatis的依赖
mysql-connector-java jdbc驱动
```

2、编写数据库配置(application.properties里面填写)
```
spring.datasource.url=jdbc:mysql://localhost:3306/learn?useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false
spring.datasource.username=root
spring.datasource.password = 123456
spring.datasource.driver-class-name = com.mysql.jdbc.Driver
```
3、定义数据库查询接口
```
@Mapper
public interface AuthorMapper {
    Author selectAuthorById(int id);
}
```
4、实现数据库查询接口（这里是用xml,新建一个XX.xml文件）
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cyq.mapper.AuthorMapper">
    <select id="selectAuthorById" resultType="com.cyq.bean.Author">
        select id, title, content from test where id = #{id}
    </select>
</mapper>
namespace是接口的 包名+类名
id 是接口的方法名（第三步中）
resultType 是查询后的返回类型，也是方法的返回类型（第三步中）

```
5、配置xml扫描的配置文件(application.properties里面填写)
```
mybatis.mapper-locations=classpath*:mybatis/*Mapper.xml
mybatis.type-aliases-package=com.cyq.bean

mybatis.mapper-locations上一步新建的xml的位置
mybatis.type-aliases-package 上一步查询结果返回值的包名
```
6、使用，测试查询
```
 @Autowired
 private AuthorMapper aaa;//注入类


 Author a= aaa.selectAuthorById(10);//调用（自己放置在需要的地方）
```

## 注意
1、依赖一定要下载完才会有效果，配置好maven，不然就是会出问题的，看看依赖有没有红线。
2、配置的maven是否正确

## 代码
[spring](/jar/spring.zip)