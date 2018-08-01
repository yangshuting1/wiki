### java代码优化 
##### 1.不要在for循环里面创建实例。容易浪费内存，而且会造成内存泄漏


##### 2. 尽量使用HashMap、ArrayList、StringBuilder，除非线程安全需要，否则不推荐使用Hashtable、Vector、StringBuffer，后三者由于使用同步机制而导致了性能开。

##### 3. 使用同步代码块替代同步方法
除非能确定一整个方法都是需要进行同步的，否则尽量使用同步代码块，避免对那些不需要进行同步的代码也进行了同步，影响了代码执行效率。


##### 4. 顺序插入和随机访问比较多的场景使用ArrayList，元素删除和中间插入比较多的场景使用LinkedList

##### 5.尽量复用对象

```java
//拼接字符串，不重视效率的写法
String str1 = "aaa";
str1 = str1 + "bbb";

//拼接字符串，效率高的写法
StringBuilder sb = new StringBuilder(str1);
sb.append("bbb");
```

##### 6. 字符串变量和字符串常量equals的时候将字符串常量写在前面 ,避免空指针异常

##### 7. 使用最有效率的方式去遍历Map
##### 8. 乘法和除法使用移位操作


##### 9.减少对变量的重复计算

```java
//避免重复计算的例子
int length = list.size();
for(int i = 0; i < length; i++){
...
}

```

##### 10.把一个基本数据类型转为字符串

```java
int id = 10;

String str = id.toString(); --最快

String str = String.valueOf(id); --次之

String str = id + “”; -- 最慢

```


### SQL优化

#### 操作符优化

##### 1. 尽量不采用IN操作符，用EXISTS 方案代替。

**Exists:先主查询，再子查询**

select num from a where exists(select 1 from b where num=a.num)


##### 2. 用Where子句替换HAVING子句

HAVING 只会在检索出所有记录之后才对结果集进行过滤. 这个处理需要排序,总计等操作. 如果能通过WHERE子句限制记录的数目,那就能减少这方面的开销.

##### 3.使用表的别名(Alias)


##### 用>=替代> 

高效: 
SELECT * FROM  EMP  WHERE  DEPTNO >=4 
低效: 
SELECT * FROM EMP WHERE DEPTNO >3 

前者直接定位到等于4的记录，
后者首先定位到DEPTNO=3的记录并且向前扫描到第一个DEPT大于3的记录。

##### 模糊查询，不要 %yue%

select*from contact where username like ‘%yue%’

会走全表扫描，除非必要，否则不要在关键词前加%，
##### 避免在 where 子句中对字段进行表达式/函数操作

##### Update语句，如果只更改1、2个字段，不要Update全部字段

##### 在新建临时表时，如果一次性插入数据量很大，那么可以使用 select into 代替 create table
避免造成大量 log ，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，然后insert。


##### 尽量避免使用游标，因为游标的效率较差，
### 数据库优化

##### 表结构优化

##### 尽量将字段定义为NOT NULL

##### 以操作频繁的字段作为查询条件，操作不频繁的字段建立索引

##### 返回更少的数据只返回要求的数据，不要全数返回

#### 

(1)数据库分页：采用数据库SQL分页需要两次SQL完成，一个SQL计算总数量，一个SQL返回分页后的数据。性能好
(2)只返回需要的字段

##### 减少交互的次数
（1） batch 操作

### 虚拟机（JVM）调优

1. 由于 Full GC 的成本远远高于 Minor GC，新对象预留在年轻代。
2. 年轻代的内存小，为了有足够的内存，将大对象进入年老代。
3. 使-Xms 和-Xmx 的大小一致获得一个稳定的堆
4. 增大吞吐量
5. 使用大的内存分页

超大文件读取 ：

1. 分页可以优化

1. 使用文件流：java.util.Scanner类扫描文件的内容
2. Apache Commons IO流：Commons IO库实现，利用该库提供的自定义LineIterator:

内存：容量较小，但数据传送速度较快
硬盘：容量大，但读写速度慢。


硬盘是持久储存数据的，断电不丢失。内存是系统程序运行中暂存数据的介质。因为CPU运行速度远远高于硬盘的读取速度。而从内存中读取速度相对硬盘直读要快很多。所以先把数据读入内存缓存，CPU调用时从内存中读取，起到CPU与硬盘速率对接的缓冲作用。
