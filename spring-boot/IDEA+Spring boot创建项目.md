# IDEA+ Spring Boot


### 新建项目

#### Stp1
new -> project -> spring Initralizr -> Next

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/1.png" alt="GitHub" title="project MeteData" width="500" height="450" />

#### Step2

填名字，剩下的都不用改

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/2.png" alt="GitHub" title="project MeteData" width="500" height="450" />

#### step3

选择依赖

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/3.png" alt="GitHub" title="project MeteData" width="500" height="450" />

#### step4

填写项目名称

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/4.png" alt="GitHub" title="project MeteData" width="500" height="450" />

#### step5

创建完成后，右键LifeStyle下的install，选择create 

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/5.png" alt="GitHub" title="project MeteData" width="500" height="450" />

#### step6 

将Commad line里面的改成：clean install -DMaven.test.skip=true

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/6.png" alt="GitHub" title="project MeteData" width="500" height="450" />


#### step7

看spring boot项目的目录，找到xxxApplication，这是整个spring boot项目的唯一入口。
添加@@RestController和一个路径


<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/7.png" alt="GitHub" title="project MeteData" width="500" height="450" />

最后在浏览器输入http://localhost:8080

<img src="https://github.com/yangshuting1/picture/blob/master/spring%20boot/8.png" alt="GitHub" title="project MeteData" width="500" height="450" />