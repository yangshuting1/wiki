## @mybatis



#### 目录：

1. 找出TopN
2. 时间范围
3. 降序+分页
4. 总和
5. 嵌套查询
6. 软删除
7. not in 操作
8. mybatis 支持set 
9. 批量插入操作，返回批量插入的主键
10. 获取日期数据
11. 实体类到数据库字段的转化

---------------------------------------------------------------------------

#### [1]:找出TopN

```xml

SELECT  k.id,
        k.keyword,
        count(rate_id) As count
FROM product_rate As r
LEFT JOIN rate_keyword_map as m ON r.id = m.rate_id
LEFT JOIN content_keyword As k ON k.id = m.keyword_id
WHERE
<!-- 条件-->
GROUP BY k.id, k.keyword ORDER BY count(rate_id) DESC LIMIT 10

```

1.限制排名前几时：使用 limit 10 √ , 不要使用范围：limit 0,9 。

2.查询出来的id和keyword是对应的，所以GROUP BY时两个都要加上。

#### [2]:时间范围

```xml

   <!-- 传进来的时间是Date类型的-->
 <if test="startTime !=null and endTime !=null">
        time between #{startTime, jdbcType=TIMESTAMP} AND
        DATE_ADD(#{endTime, jdbcType=TIMESTAMP}, INTERVAL 1 DAY)
 </if>

```


#### [3]：降序+分页

```xml

SELECT a
FROM b
WHERE deleted = 0
ORDER BY cc DESC
LIMIT #{startIndex}, #{pageSize}

```

#### [4]: 总和

```xml

 SELECT count(*)

```

#### [5]: 嵌套查询

先查出类别，根据类别id去查这个类别下所有的关键词

```xml

 <resultMap id="KeywordDetailResult" type="KeywordDetailDto">
        <id property="typeId" column="id"/>
        <result property="typeName" column="name"/>
        <collection property="words" column="id" ofType="Keyword" select="findKeywordByTypeId"/>
    </resultMap>

    <select id="findKeywordByTypeId" resultType="keyword">
        SELECT *
        FROM content_keyword k 
        LEFT JOIN content_keyword_type_map m ON k.id = m.keyword_id
        WHERE k.deleted = 0 AND m.type_id = #{id}
    </select>

```

#### [6]:软删除（将原本的数据进行修改）

```xml
 <update id="delete" parameterType="list">
        UPDATE table
        SET  column = CONCAT(column ,'_deleted_',UNIX_TIMESTAMP())
        WHERE id = n;
    </update>
```
#### [7]：not in 操作

```xml

 <update id = "deleteRatesNotInUrlIds">
        UPDATE product_rate
        SET deleted = 1
        WHERE product_id = #{productId} AND deleted = 0 AND url_id not in
        <foreach collection="list" index="index" item="item" open="(" close=")" separator=",">
            #{item}
        </foreach>
    </update>

```


#### [8]：mybatis 支持set


parameterType要写成collection


#### [9]: 批量插入操作，返回批量插入的主键

要加上useGeneratedKeys="true" keyProperty="id"
就可以在返回时携带上批量增加的主键

```java

  <insert id="insertUrl" useGeneratedKeys="true" keyProperty="id" parameterType="PlatformProductUrl">
        INSERT INTO platform_product_url (platform_id, product_id, url, deleted, create_time)
        VALUES (#{platformId}, #{productId}, #{url}, #{deleted}, now())
    </insert>


```

#### 10.获取日期数据

1. 参数类型： LocalDate 
 
**用between and 效率最优**
 
```java

//获取的是syncDate这天的所有数据

create_time BETWEEN #{syncDate} AND date_add(#{syncDate}, interval 1 day)

```

2. 参数类型： String（2018-01-12）

```java

//效率较低，会全局遍历所有的create_time

date(create_time) = #{syncDate}

```

3. 查找当天的

```java

create_time = curdate()

```


4. 存储当天的零点零零分 

``` java

set create_time = date_format(now(),'%y-%m-%d')

// create-time:2018-01-01 00:00:00

```

```java


```

#### 11.实体类到数据库字段的转化


  src/main/resources/mybatis-config.xml
  
```java
    <setting name="mapUnderscoreToCamelCase" value="true"/>
```