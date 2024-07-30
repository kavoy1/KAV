# application.properties

## 数据库连接

```
# 数据库连接池类型
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
# 数据库连接配置
spring.datasource.url=jdbc:mysql://localhost:3306/your_database
spring.datasource.username=your_username
spring.datasource.password=your_password
# 数据库驱动类名
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

## 文件上传大小配置

```
#单个文件大小限制
spring.servlet.multipart.max-file-size=10MB
#单个请求大小限制
spring.servlet.multipart.max-request-size=100MB
```

## MyBatis 配置
```
#开启mybatis的驼峰命名自动映射开关
mybatis.configuration.map-underscore-to-camel-case=true
#当设置为 true 时，这个选项告诉 MyBatis 在执行插入操作后，使用数据库生成的主键。
mybatis.configuration.use-generated-keys=true
这个设置用于定义 MyBatis 执行数据库操作时的超时时间（以秒为单位）。
mybatis.configuration.default-statement-timeout=25
```





# XML映射文件

## 规范



- XML映射文件的名称与Mapper接口名称一致，并且将XML映射文件和Mapper接口放置在相同包下(同包同名)
- XML映射文件的namespace属性为Mapper接口全限定名一致。
- XML映射文件中sql语句的id与Mapper 接口中的方法名一致，并保持返回类型一致。

## 约束

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

## 配置

```
<mapper namespace="com.fxy.mapper.stumapper"> //namespace 是mapper接口地址
    <select id="stuSelect2" resultType="com.fxy.pojo.student"> //id 是方法名 ，resultType 是封装单条数据的类型 单条！！！
        select * from db01.student where name like concat('%',#{name},'%')
    </select>
</mapper>
```

## if标签



```
<select id="selectUsers" resultType="User">
  SELECT * FROM users
  <if test="username != null and username.trim() != ''">
    WHERE username = #{username}
  </if>
  <if test="status != null">
    AND status = #{status}
  </if>
</select>
```



标签用于在 SQL 语句中实现条件判断。它类似于 Java 中的 if 语句。你可以在 标签中指定一个条件，当条件为真时，包含在 标签内的 SQL 片段会被执行。

# 

```
<foreach item="item" index="index" collection="collection" open="(" separator="," close=")">
    #{item}
</foreach>
```



- item: 表示当前遍历到的元素。
- index: 可选，表示当前元素的索引。
- collection: 需要遍历的集合。
- open: 可选，开始的字符串。
- separator: 可选，元素之间的分隔符。
- close: 可选，结束的字符串。 需要注意的是，collection 可能需要搭配mapper接口层 传参使用注释@Param（""），XML文件才能映射到集合







## SQL片段的抽取和引用



### 定义 SQL 片段



你可以在 MyBatis 映射文件的任何地方定义 SQL 片段，通常放在文件的顶部：

```
<sql id="baseStudentColumns">
    id, name, age, class_id
</sql>
```



在这个例子中，我们定义了一个名为 baseStudentColumns 的 SQL 片段，它包含了一些列名。

### 引用 SQL 片段



一旦定义了 SQL 片段，你可以在其他 SQL 语句中通过 标签引用它：

```
<select id="selectStudents" resultType="Student">
    SELECT <include refid="baseStudentColumns" />
    FROM student
    WHERE class_id = #{classId}
</select>
```



## 关于XML中SQL_DML

- SELECT

```
    <select id="stuSelect3" resultType="com.fxy.pojo.student">/*id是方法名，resultType是返回的单个数据的数据类型*/
        select *
        from db01.student
        <where>
            <if test="name!=null">name like concat('%', #{name}, '%')</if>
            <if test="gender!=null">and gender = #{gender}</if>
        </where>
    </select>
```



- INSERT

```

```



- DELETE

```
    <delete id="stuDelete2">
        delete
        from db01.student
        where id in
        <foreach collection="ids" item="id" separator="," open="(" close=")">
            #{id}
        </foreach>
    </delete>
```



- UPDATE

```
    <update id="stuUpdate2">
        UPDATE db01.student
        <set>
            <if test="username!=null">username = #{username},</if>
            <if test="name!= null">name =#{name},</if>
            <if test="age!=null">age = #{age},</if>
            <if test="gender!=null">gender = #{gender}</if>
        </set>
        WHERE id = #{id}
    </update>
```



- 动态sql

```
    <select id="testSl" resultType="com.fxy.pojo.stu">
        SELECT * FROM db01.stu WHERE id IN
        <foreach collection="ids" item="id" open="(" separator="," close=")">/*collection 是传入进来的参数名 ，item是遍历出来为每个数据命名的名称*/
            #{id}
        </foreach>
    </select>
```







# 依赖坐标

## Lombok



```
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.20</version> <!-- 请使用最新的版本号 -->
        <scope>provided</scope>
    </dependency>
```



## spring web



```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
```



## Druid



```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.8</version> <!-- 请检查是否有更新的版本 -->
</dependency>
```



## OSS-SDK



在Maven工程中使用OSS Java SDK，只需在pom.xml中加入相应依赖即可。以在中加入3.17.4版本的依赖为例：

说明 建议优先使用最新版本的依赖项。关于Java SDK最新版本相关信息，请参见[GitHub](https://github.com/aliyun/aliyun-oss-java-sdk)。

```
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.17.4</version>
</dependency>
```



如果使用的是Java 9及以上的版本，则需要添加JAXB相关依赖。添加JAXB相关依赖示例代码如下：

```
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>
<!-- no more than 2.3.3-->
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>2.3.3</version>
</dependency>
```