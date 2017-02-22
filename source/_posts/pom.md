title: pom
date: 2017-02-22 17:44:42
tags: spring
---
# pom文件

project  根目录
```
<modelVersion>4.0.0</modelVersion>
```
modelVersion:描述这个POM文件遵从哪个版本的项目描述符（4.0标示使用maven2）

```
 <groupId>com.renrun.supplychain</groupId>
 <artifactId>ucenter</artifactId>
 <version>1.0-SNAPSHOT</version>
```
groupId:一般是企业网址，或者包名，组织的标示
artifactId：项目名
groupId 和 artifactId 合起来作为当前项目的唯一标识
version：项目版本号

```
<parent>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-parent</artifactId>
   <version>1.4.3.RELEASE</version>
</parent>
```
父级依赖，框架父级已经帮我们定义了很多的依赖了，这样可以减少引入

```
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <version.spring>3.2.9.RELEASE</version.spring>
        <version.jackson>2.8.5</version.jackson>
        <java.version>1.7</java.version>
    </properties>
```
定义变量，统一配置

```
  <dependencies>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${version.jackson}</version>
        </dependency>
        <dependency>
            <groupId>com.renrun.supplychain</groupId>
            <artifactId>ucenter-share</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
  <dependencies>
```
自定义依赖，可以在dependencies中配置多个dependency
${version.jackson}使用配置
groupId,artifactId,version定义唯一的依赖要求。
scope依赖适用的范围


```
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Brixton.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

dependencies即使在子项目中不写该依赖项，那么子项目仍然会从父项目中继承该依赖项（全部继承）
dependencyManagement里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

```
<plugins>
   <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
   </plugin>
</plugins>
```
为了创建可执行的jar
