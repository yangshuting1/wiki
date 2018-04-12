## LocalDateTime

### 简介

JAVA 8 引入新时间API原因是原来的Date类无法支持多线程操作，新时间API支持多线程操作，当java.time包内时间类其值发生改变时，其如同String类，类的实例是不可变的对象，当改变其值的时候就会新生成对象地址，从而改变其对象地址，以保证支持多线程操作。

### 获取当前的localDateTime

```java

LocalDateTime localDateTime = LocalDateTime.now();

// 结果： 2018-01-23T14:24:15.202

```

### LocalDateTime -> LocalDate

```java

LocalDate localDate = localDateTime.toLocalTime();

// 结果：2018-01-24

```

### LocalDateTime -> timeStamp

```java

Timestamp timestamp = Timestamp.valueOf(localDateTime)

// 结果：2018-01-24 10:38:46.951

```

### String（不规范） -> LocalDateTime

```java

String date1 = "2018-03-28 10:40:14.0";
try {
    LocalDateTime localDateTime = Timestamp.valueOf(date1).toLocalDateTime();
    DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    System.out.println(localDateTime.format(dtf));
} catch (Exception e){
    e.printStackTrace();
}

```

### long -> LocalDateTime

```java

  LocalDateTime createTime = LocalDateTime.ofEpochSecond(onSaleTime / 1000, 0, ZoneOffset.ofHours(8));

```

### mybatis中的应用

添加依赖：

```java

<dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-typehandlers-jsr310</artifactId>
            <version>1.0.2</version>
        </dependency>

```

entity:

```java

public class QueryCompareSpuDto {
     
    //这样会自动将参数转换
    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE)
    private LocalDate startTime;

    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE)
    private LocalDate endTime;


```

```java


    @RequestMapping(value = "/search_spu", method = RequestMethod.GET)
    @ResponseBody
    public Result<List<YanxuanSpuDetail>> findSpuByDate(@RequestParam(value = "syncDate") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate syncDate) {
        List<YanxuanSpuDetail> yanxuanSpuList = yanxuanSpuService.findSpuByDate(syncDate);
        return new Result.Builder<>(yanxuanSpuList).setCode(ErrorCode.SUCCESS).build();
    }
```


获取当天数据：

1.时间区间用：between LocalDate and date_add(LocalDate, interval 1 day)

```java

create_time BETWEEN #{syncDate} AND date_add(#{syncDate}, interval 1 day)

```

