# MyBatis[^1]
[^1]:优秀的持久型框架,大大的简化了JDBC代码和参数的手工设置

+ MyBatis中命名空间（namespace）的作用?
> 为每个映射文件起一个唯一的命名空间,避免起冲突.,就像每一个sql语句都有唯一的ID,


## 掌握点
1. `Mybatis`的理解,作用
2. 自动生成器generator的使用
3. 各种标签元素,如`resultMap`,`result`,`collection`,`assocition`等
4. 一对多,多对一的关系
5. 动态SQL(重要)
6. 缓存
## 表关联的**一对多**,<collection></collection>
> pojo类: User,Posts
```java
public class User{
  private Integer id;
  private String username;
  private String mobile;
  // 对象集合
  private  List<Posts> posts;
}

public class Posts{
  private Integer id;
  private String title;
  private String content;
}

```
> Mapper.xml的映射
```xml
<!--user表有一个post对象类型的集合,一个作者有很多帖子-->
<resultMap type="User" id="resultUserMap">
    <result property="id" column="user_id" />
    <result property="username" column="username" />
    <result property="mobile" column="mobile" />
    <collection property="posts" ofType="类名" column="userid">
		<!-- post对象属性 -->
		<id property="id" column="post_id" javaType="int" jdbcType="INTEGER"/>    
        <result property="title" column="title" javaType="string" jdbcType="VARCHAR"/>
        <result property="content" column="content" javaType="string" jdbcType="VARCHAR"/> 
	</collection>
</resultMap>
```

## 表关联的**多对一**,<association></association>
> pojo类
```java
public class Posts{
  private Integer id;
  private String title;
  private String content;
  // 类对象
  private User user;
}
```
```xml
<!--post表有一个user对象,很多帖子有同一个作者-->
<resultMap type="Post" id="resultPostsMap">
    <result property="id" column="post_id" />
    <result property="title" column="title" />
    <result property="content" column="content" />
    <!-- 作者对象属性-->
    <association property="user" javaType="User">  
    <id property="id" column="userid"/>   
    <result property="username" column="username"/>   
    <result property="mobile" column="mobile"/>   
    </association> 
</resultMap>
```

## 动态SQL语句
+ `if`
  
  > 场景:有条件判断
```xml
SELECT * FROM BLOG WHERE state = 'ACTIVE'
<if test="title != null and title !=''">
    AND title like #{title}
</if>
```

+ `choose`, `when`, `otherwise`
  
  > 场景:二选一
```xml
SELECT * FROM BLOG WHERE state = 'ACTIVE'
<choose>
<!-- when如果有一个成立，则 choose 结束 -->
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
</choose>
```

+ `where`
  
  > 场景:带where操作
```xml
SELECT * FROM BLOG
<!-- where标签有返回值就会插入where,但如果返回的内容是AND或OR开头的就会剔除and/or -->
<where>
<if test="state != null">
    state = #{state}
</if> 
<if test="title != null">
    AND title like #{title}
</if>
<if test="author != null and author.name != null">
    AND author_name like #{author.name}
</if>
</where>
```

+ `trim`
  
  > 场景:自定义的实现功能,应用广泛
```xml
<!-- prefixOverrides:前缀覆盖/忽略,包含内容的首部某些内容去除,
suffixOverrides:后缀覆盖/忽略,包含内容的尾部去除,
prefix:前缀,插入属性中指定的内容
suffix:后缀,插入属性中指定的内容
-->
insert into smbms_user
<trim prefix="(" suffix=")" suffixOverrides=",">
    <if test="pkId != null">
    pk_id,
    </if>
    <if test="fkRoleId != null">
    fk_role_id,
    </if>
</trim>
<trim prefix="values (" suffix=")" suffixOverrides=",">
    <if test="pkId != null">
    #{pkId,jdbcType=BIGINT},
    </if>
    <if test="fkRoleId != null">
    #{fkRoleId,jdbcType=BIGINT}
    </if>
</trim>

<!-- trim代替where-->
SELECT * FROM user u
<trim prefix="WHERE" prefixOverrides="AND|OR">  
  <if test="username !=null ">  
   u.username LIKE CONCAT('%', #{username, jdbcType=VARCHAR}),'%')  
  </if>  
  <if test="sex != null and sex != '' ">  
    AND u.sex = #{sex, jdbcType=INTEGER}  
  </if>  
  <if test="birthday != null ">  
    AND u.birthday = #{birthday, jdbcType=DATE}  
  </if>
</trim>
```

+ `set`
  
  > 场景:set操作,通常update语句
```xml
<!-- set 元素会动态前置 SET 关键字，同时也会消除无关的逗号-->
update Author
  <set>
    <if test="username != null">
        username=#{username},
    </if>
    <if test="password != null">
        password=#{password},
    </if>
    <if test="email != null">
        email=#{email},
    </if>
    <if test="bio != null">
        bio=#{bio}
    </if>
  </set>
where id=#{id}
```

+ `foreach`
  
  > 场景:对一个集合进行遍历，通常是在构建 IN 条件语句
```xml
 SELECT *
  FROM POST P
  WHERE ID in
  <!-- item:当前迭代元素值,index:索引 collection:集合为list,数组为array -->
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

+ `bind`
  
  > 场景:模糊查询 like  '  '
```xml
<bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
```

+ `#{}`与`${}`
  > #{}:相当于创建一个PreparedStatement 参数占位符`?`能安全的设置参数防止sql注入
  > ${}:不会修改也不会转义字符串,如:ORDER BY ${columnName}
```java
@Select("select * from user where id = #{id}")
User findById(@Param("id") long id);
```

## Mybatis缓存
+ 缓存作用: 合理使用缓存是常见的优化手段,将从数据库中查询的数据放入缓存中,下次使用时不必再次查询,直接从缓存中取出,避免频繁的操作数据库,减轻数据库的压力,提高性能

+ mybatis缓存
> 默认情况下，只启用了本地的会话缓存,要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：<cache ..../>语句,示例
```xml
<!-- eviction:清除策略
	 flushInterval:刷新间隔,单位:毫秒
	 size:引用数目
	 readOnly:只读,默认false
-->
<!-- xxxSqlMqpp.xml -->
<cache
  eviction="FIFO"
  flushInterval="60000" 
  size="512" 
  readOnly="true">
</cache>
  
  <select ... flushCache="false" useCache="true"/>
```
> 效果:
> 映射语句文件中的所有 select 语句的结果将会被缓存。
> 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
> 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
> 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
> 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
> 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改  

> 清除策略:
> `LRU` – 最近最少使用：移除最长时间不被使用的对象。
> `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
> `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
> `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

### 一级缓存
+ **介绍:**
一级缓存是SqlSession级别的缓存,在操作数据库时需要构造sqlSession对象,不同的sqlSession之间的缓存数据区域是互相不影响的
+ **工作原理:**
第一次查询操作,会写入一级缓存区域,修改,新增,删除执行
commit后会清空 缓存,第二次查询时从缓存里读取
sqlSession执行commit，即增删改操作时会清空缓存。这么做的目的是避免脏读

  如果commit不清空缓存，会有以下场景：A查询了某商品库存为10件，并将10件库存的数据存入缓存中，之后被客户买走了10件，数据被delete了，但是下次查询这件商品时，并不从数据库中查询，而是从缓存中查询，就会出现错误

### 二级缓存
+ **介绍:**
二级缓存(按namespace划分)是mapper级别的缓存，多个SqlSession去操作同一个Mapper的sql语句，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。二级缓存的作用范围更大
  
  两个mapper的namespace如果相同，这两个mapper执行sql查询到数据将存在相同的二级缓存区域中。

+ **开启二级缓存**
1. 打开总开关
```xml
<!--Mybtais配置文件中加入-->
<settings>  
<!--开启二级缓存,默认是关闭的-->  
<setting name="cacheEnabled" value="true"/>  
</settings> 
```
2. 在需要开启二级缓存的mapper.xml中加入`<caceh />`标签
3. 让使用二级缓存的POJO类实现Serializable接口
```java
  public class User implements Serializable {}
```
## 总结
+ 对于查询多commit少且用户对查询结果实时性要求不高，此时采用mybatis二级缓存技术降低数据库访问量，提高访问速度

+ 二级缓存是建立在同一个namespace下的，如果对表的操作查询可能有多个namespace，那么得到的数据就是错误的

+ **举例: **订单和订单详情,orderMapper.xml和orderDetailsMapper.xml,当查询订单信息时也需要查询订单的详细信息,查询的订单详细信息被二级缓存在orderDetailsMapper的namespace中,当需要修改订单信息时,会修改orderMapper的namespace,与订单详情的缓存无关,第二次查询**订单详情**时,从缓存里取出的数据是过时的,不是订单修改后对应的订单详情信息

+ **所以,①对单表的操作与查询都应该在同一个namespace下,若其他的namespace有操作会发生数据脏读**

  **②对关联表的查询,关联的所有表的操作都必须在同一个namespace。**

