---
  title: 【mybatis踩坑】
  date: 2018-08-09
---

# mybatis

## 踩坑点

* Group by
* Having与Where的区别
* and/or

## content

#### 1. Group by

**Group by:仅适用于根据一个或多个列对结果集进行分组**

Group by:主要用于对数据进行分组

除聚合函数中的字段，剩下select所有的字段都必须在group by中出现。

#### 2. Having与Where的区别

where： 在对查询结果进行分组前，将不符合where条件的行去掉，即在分组之前过滤数据，where条件中不能包含聚组函数，使用where条件过滤出特定的行。

having：是筛选满足条件的组，即在分组之后过滤数据，条件中经常包含聚组函数，使用having 条件过滤出特定的组，也可以使用多个分组标准进行分组。

```sql

select 类别, sum(数量) as 数量之和 from A
group by 类别
having sum(数量) > 18

```

#### 3. and/or

出题：模糊匹配标题或编码

错误示范：

```sql
SELECT DISTINCT o.id, img, status, protect_rights_num
FROM store_protect_rights_work_order o
LEFT JOIN platform_store s ON s.id = o.store_id
WHERE o.deleted = 0
<if test="fuzzyName != null and fuzzyName != ''">
    AND title LIKE concat('%', #{fuzzyName}, '%' OR protect_rights_num LIKE concat('%', #{fuzzyName}, '%'))
</if>

```

正确示范： 

```sql
# 应在外层加(),这样才是一个整体
(AND title LIKE concat('%', #{fuzzyName}, '%' OR protect_rights_num) LIKE concat('%', #{fuzzyName}, '%'))
</if>

