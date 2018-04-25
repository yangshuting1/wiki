# Freemarker 模板引擎

## 目录

* 概念
* 常用类型及条件
* 实践 
* 三种获取内置模板
* 获取外部模板

## content:

### 1. 概念

是一款模板引擎，用来将html和后端数据结合在一起使用的通用类库。其实是一个java类库。

html模板 + 后端数据 = 输出

 
### 2. 常用类型及条件

**常用类型:** 

1. 值：${key}表示 
2. 对象：${object.属性}


##### 1. if else 

```html

<#if condition>
...
<#elseif condition2> 
...
<#else>
...
</#if>  <!--一定要加的-->
...
```

##### 2. gt/gte 

```html
<#if x == 0>
...
<!-- 用gt代表大于 -->
<#elseif x gt 0 >
...
<!-- 用gte 代表小于 -->
<#elseif x gte 0>
...
```

##### 3. list

```html
<!-- 判断数组是否等空 -->

<#if list?? && (list?size > 0)>
...
</#if>

<!-- 循环数组 -->
 <#list categoryList as categorys>

```


### 3. 实践


创建Configuration实例 + 数据模板（Map）+ 获取模板 + 合并
 
```java

try {
    //1. 创建一个合适的Configration对象
    Configuration cfg = new Configuration(); 
    cfg.setDefaultEncoding("UTF-8");  //必须  
    // 2. 数据模板
    Map<String, Object> paramMap = new HashMap<>();
    paramMap.put("list", categoryList);
    paramMap.put("date", LocalDateTime.now().toLocalDate());
    // 获取模板 
    ....
    // 合并
    Writer writer = new OutputStreamWriter(new FileOutputStream("success.ftl"), "UTF-8");
    template.process(paramMap, writer);
    // 将html输出
    String htmlText = FreeMarkerTemplateUtils.processTemplateIntoString(template, paramMap);
} catch (TemplateException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}

```

模板加载器的方式：
1. 内建模板加载器  ok
2. 从多个位置加载
3. 从其他资源加载  ok
4. 模板路径

### 4. 三种内建模板加载器

##### 1. setDirectoryForTemplateLoading(File dir) 利用本地路径加载模板


本地模板路径：src/main/webapp/WEB-INF/template/model.ftl

```java

// 把磁盘的路径来撸下来，如果要上测试，线上环境就不兼容了
configuration.setDirectoryForTemplateLoading(new File(
C:\Users\xxx\Desktop\project\src\main\webapp\WEB-INF\template));

Template template = configuration.getTemplate("model.ftl");

```

##### 2. setClassForTemplateLoading(Class cl, String prefix) 类加载机制

1. 是通过类加载机制来加载模板的方式。
2. 传入的class参数会被 Class.getResource() 用来调用方法来找到模板。
3. 参数 prefix 是加前缀的。
4. 从类路径下加载文件的这种机制，要比从文件系统的特定目录位置加载安全而且简单。

本地模板路径：src\main\resources\template\model.ftl

this.class 路径：src\main\java\com\xxx\xxx\xxx\xxx\util\Util.java

```java

configuration.setClassForTemplateLoading(this.getClass(), "/template");
Template template = configuration.getTemplate("model.ftl");

```

##### 3. setServletContextForTemplateLoading(Object servletContext, String path)  应用上下文和基路径 （如果不在webapp下是无法加载的）

基路径：Web应用根路径(WEB-INF目录的上级目录)的相对路径

加载应用上下文

```java

// 1. 依赖注入serverContext
@AutoWried
ServerContext serverContext;

// 2. 利用webApplication
WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
ServletContext context = webApplicationContext.getServletContext();

```

本地模板路径：src/main/webapp/WEB-INF/template/model.ftl

```java

//默认的路径就在WEB-INF
configuration.setClassForTemplateLoading(serverContext, "/template");

Template template = configuration.getTemplate("model.ftl");

```

### 5. 从其他资源加载：

#####  1. 需要自己写类加载器来继承URLTemplateLoader

RemoteTemplateLoader.java

```java

public class RemoteTemplateLoader extends URLTemplateLoader {
    // 远程模板文件的存储路径（目录）
    private String remotePath;
    private static String yanxuanMailTemplate = "http://";

    public RemoteTemplateLoader(String remotePath) {
        if (remotePath == null) {
            throw new IllegalArgumentException("remotePath is null");
        }
        this.remotePath = canonicalizePrefix(remotePath);
        if (this.remotePath.indexOf('/') == 0) {
            this.remotePath = this.remotePath.substring(this.remotePath.indexOf('/') + 1);
        }
    }

    @Override
    protected URL getURL(String name) {
        name = name.replace("_zh_CN", "");  //不知道为啥生成的总会有这个，所以replace掉了
        String fullPath = this.remotePath + name;
        System.out.println(fullPath);
        if ((this.remotePath.equals("/")) && (!isSchemeless(fullPath))) {
            return null;
        }
        URL url = null;
        try {
            url = new URL(fullPath);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return url;
    }

    private static boolean isSchemeless(String fullPath) {
        int i = 0;
        int ln = fullPath.length();

        if ((i < ln) && (fullPath.charAt(i) == '/')) {
            i++;
        }

        while (i < ln) {
            char c = fullPath.charAt(i);
            if (c == '/') {
                return true;
            }
            if (c == ':') {
                return false;
            }
            i++;
        }
        return true;
    }
}

```

##### 2. 获取模板 setTemplateLoader() 获取外部路径

```java

//创建Configration对象
Configuration cfg = new Configuration();

//创建一个获取外部url的
RemoteTemplateLoader remoteTemplateLoader = new RemoteTemplateLoader(yanxuanMailTemplate);
cfg.setTemplateLoader(remoteTemplateLoader);

Template template = cfg.getTemplate("/model.ftl");

```



