## jackson



### 简介

是用来序列化和反序列化的java框架,是最流行的 json 解析器之一 。

#### 优势

1. 依赖的jar包少
2. 解析大的 json 文件速度比较快
3. 运行时占用内存比较低，性能比较好
4. 有灵活的API,可以很容易进行扩展和定制


### 使用

#### 1. 添加pom.xaml配置

```java

<dependency>
    //核心包：高性能的流模式来生成和解析json
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
    </dependency>
    //数据绑定
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
    //注解包，提供标准注解功能
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-annotations</artifactId>
    </dependency>
    //高阶：处理时间格式
    <dependency>
        <groupId>com.fasterxml.jackson.datatype</groupId>
        <artifactId>jackson-datatype-jsr310</artifactId>
</dependency>

```

#### 2. 常用注解


##### @JsonProperty : 属性转换

```java

 @JsonProperty("对应json") 
 private Date birthDate;

```

##### @JsonFormat :在序列化时转化成指定格式

```java

//用于返回前端的数据格式
@JsonFormat(pattern = "yyyy-MM-dd")
private String createTime;


@JsonFormat(pattern = "yyyy-MM-dd")
private LocalDate startTime;
```


##### @JsonIgnore: 展示时不显示

```java

@JsonIgnore
private int deleted;

```

##### 高阶：@JsonDeserialize 

```java

@JsonDeserialize(using = UnixTimestampDeserializer.class)
private LocalDateTime displayTime;

```

#### 3. 通过注解方式使用

使用：

```java

    @Autowired
    private ObjectMapper objectMapper;
    //Xx x = objectMapper.readValue(json, xx.class);
    TaobaoJsonDataDto taobaoJsonDataDto = objectMapper.readValue(jsonString, TaobaoJsonDataDto.class);

```

entity：由于可能只是需要json数据的一部分，可以定义一个内部类

```java

@JsonIgnoreProperties(ignoreUnknown = true)
@JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy.class)
@JsonInclude(JsonInclude.Include.NON_NULL)
public class TaobaoJsonDataDto implements Serializable {

    private static final long serialVersionUID = 6445264713838158675L;
    
    private TaobaoModsDto mods;
    
    //get/set/toString
    
    @JsonIgnoreProperties(ignoreUnknown = true)
    @JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy.class)
    @JsonInclude(JsonInclude.Include.NON_NULL)
    public static class TaobaoModsDto implements Serializable {
    
        private static final long serialVersionUID = 6445264713838158675L;
        
        private TaobaoItemListDto itemlist;
        
        private TaobaoPagerDto pager;
        
        //get/set/toString
    }
    
    @JsonIgnoreProperties(ignoreUnknown = true)
    @JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy.class)
    @JsonInclude(JsonInclude.Include.NON_NULL)
    public static class TaobaoItemListDto implements Serializable {
    
        private static final long serialVersionUID = 6445264713838158675L;
        
        private TaobaoSearchItemDto data;
        
        //get/set/toString
    }
}


四种策略：

CamelCase策略，Java对象属性：personId，序列化后属性：persionId

PascalCase策略，Java对象属性：personId，序列化后属性：PersonId

SnakeCase策略，Java对象属性：personId，序列化后属性：person_id

KebabCase策略，Java对象属性：personId，序列化后属性：person-id



```

