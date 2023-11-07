## 在springBoot中的配置

> [超简单，Spring boot 配置mybatis](https://blog.csdn.net/zhoujiyu123/article/details/79786847)

`mybatis`的配置分为三种方式：`mybatis-config.xml`、`application.properties` 、`applications.yaml`

其中着重介绍 `applications.yaml`中`mybatis`的配置，相关配置如下：

```yaml
spring:
  # 以下是关于数据源的配置，其中使用了数据库连接池Druid
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/runoob
    type: com.alibaba.druid.pool.DruidDataSource
    
# mybatis
mybatis-plus:
  # mapper.xml文件所在的路径
  mapper-locations: classpath*:/mapper/**/*.xml
  #实体扫描，多个package用逗号或者分号分隔
  # 一般在domain中抽象程序中使用的实体
  typeAliasesPackage: com.example.domain
  typeEnumsPackage: com.example.domain.enums.**
  global-config:
    #数据库相关配置
    db-config:
      #主键类型  AUTO:"数据库ID自增", INPUT:"用户输入ID", ID_WORKER:"全局唯一ID (数字类型唯一ID)", UUID:"全局唯一ID UUID";
      id-type: AUTO
      #字段策略 IGNORED:"忽略判断",NOT_NULL:"非 NULL 判断"),NOT_EMPTY:"非空判断"
      field-strategy: NOT_NULL
      #驼峰下划线转换
      column-underline: true
      logic-delete-value: 1
      logic-not-delete-value: 0
    banner: false
  #原生配置
  configuration:
    # map-underscore-to-camel-case: false
    default-enum-type-handler: org.apache.ibatis.type.EnumOrdinalTypeHandler
    cache-enabled: false
    call-setters-on-nulls: true
    jdbc-type-for-null: 'null'
```

### 配置中的注意事项

主要注意mapper-locations、typeAliasesPackage以及typeEnumsPackage三个变量的配置。否则App启动后无法找到后续创建的mappre和抽象实体。

## Mybatis基础语法规则

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 <mapper namespace="com.mucfc.model.EmployeerMapper">
 
     <!-- 查找 -->
     <select id="findEmployeerByID" parameterType="int" resultType="Employeer">
     select* from `t_employeer` where employeer_id =#{employeer_id}
     </select>
     
      <!-- 插入 -->  
      <!-- useGeneratedKeys设置为"true"表明要MyBatis获取由数据库自动生成的主键；keyProperty="id"指定把获取到的主键值注入到Employeer的id属性 --> 
    <insert id="addEmployeer" parameterType="Employeer"
    useGeneratedKeys="true" keyProperty="employeer_id">  
        insert into `t_employeer`(employeer_name,employeer_age,employeer_department,employeer_worktype) 
        values(#{employeer_name},#{employeer_age},#{employeer_department},#{employeer_worktype})  
    </insert>  
    
     <!-- 删除  -->
    <delete id="deleteEmployeer" parameterType="int">  
        delete from `t_employeer` where employeer_id = #{employeer_id}  
    </delete>  
    
    <!-- 修改 -->  
    <update id="updateEmployeer" parameterType="Employeer">  
        update t_employeer set employeer_name = #{employeer_name},employeer_age= #{employeer_age},employeer_department = #{employeer_department}
        ,employeer_worktype=#{employeer_worktype}  where employeer_id = #{employeer_id}  
    </update>  
    
 </mapper>
```

**注意事项：**

- namespace要和定义的interface匹配
- 每个sql语句的id要和interface中定义的方法名匹配

## Mybatis原理

- Configuration MyBatis所有的配置信息都保存在Configuration对象之中，配置文件中的大部分配置都会存储到该类中
- SqlSession 作为MyBatis工作的主要顶层API，表示和数据库交互时的会话，完成必要数据库增删改查功能
- Executor MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
- StatementHandler 封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数等
- ParameterHandler 负责对用户传递的参数转换成JDBC Statement 所对应的数据类型
- ResultSetHandler 负责将JDBC返回的ResultSet结果集对象转换成List类型的集合
- TypeHandler 负责java数据类型和jdbc数据类型(也可以说是数据表列类型)之间的映射和转换
- MappedStatement MappedStatement维护一条<select|update|delete|insert>节点的封装
- SqlSource 负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回
- BoundSql 表示动态生成的SQL语句以及相应的参数信息

![](https://github.com/MENGHAH/TravelonBooks/blob/main/images/mybatis/Mybatis-structure.png?raw=true)

### Mybatis执行流程

总体执行流程就是对JDBC的封装
**(1) 加载配置并初始化**

**触发条件：**加载配置⽂件

配置来源于两个地⽅，⼀个是配置⽂件(主配置⽂件conf.xml,mapper⽂件*.xml),—个是java代码中的注解，将主配置⽂件内容解析封装到Configuration,将sql的配置信息加载成为⼀个mappedstatement对象，存储在内存之中

**(2) 接收调⽤请求**

触发条件：调⽤Mybatis提供的API

传⼊参数：为SQL的ID和传⼊参数对象

**处理过程：**将请求传递给下层的请求处理层进⾏处理。

**(3) 处理操作请求**

**触发条件：**API接⼝层传递请求过来

传⼊参数：为SQL的ID和传⼊参数对象

处理过程：

(A) 根据SQL的ID查找对应的MappedStatement对象。

(B) 根据传⼊参数对象解析MappedStatement对象，得到最终要执⾏的SQL和执⾏传⼊参数。

(C) 获取数据库连接，根据得到的最终SQL语句和执⾏传⼊参数到数据库执⾏，并得到执⾏结果。

(D) 根据MappedStatement对象中的结果映射配置对得到的执⾏结果进⾏转换处理，并得到最终的处理结果。

(E) 释放连接资源。

**(4) 返回处理结果**



## 其他

**1.个人对Mybatis的理解**

mybatis的最终操作对象是数据库，他是对Connection JDBC的高级封装。mybatis所有的操作最后都是调用jdbc的api对数据库进行操作。

**2.MyBatis 和 Druid的关系？**

**Druid 和MyBatis 是两种不同的技术，但是可以配合使用**。 Druid 是一个开源的**数据库连接池**，被广泛应用在Java 项目中。 MyBatis 是一个开源的持久层框架，它可以将Java 程序与关系型数据库进行映射。

使用 Druid 时，我们可以将 Druid 用作数据库连接池，来管理数据库连接，这样可以优化应用程序的性能。而使用 MyBatis 时，我们可以将其用作持久层框架，来进行数据库操作。因此，Druid 和 MyBatis 可以结合使用，以实现一个高效的数据库访问方案。

## 参考

1. [Mybatis运行原理](https://zhuanlan.zhihu.com/p/97879019)
2. 