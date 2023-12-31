# 开发指南

## 导入TelemetrySDK-Logger(Java)
**第1步** 检查版本兼容性
Logger
- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Java版本是否符合要求。
- 在pom.xml中引入相应版本的sdk包
-
-  opentelemetry-exporter-ar-log组件 需要依赖 opentelemetry-exporter-common组件
-  我们一共提供了有四个包：
```
  opentelemetry-exporter-common-2.4.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-common-2.4.0-jar-with-dependencies.jar //大包，包含了所有依赖包
  opentelemetry-exporter-ar-log-2.4.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-ar-log-2.4.0-jar-with-dependencies.jar   //大包，包含了所有依赖包，包括opentelemetry-exporter-common
```

### 导包方法（根据部署环境选择以下三种方法的一种即可）
#### 1.1 最佳实践：【离线环境下可以使用】
###### 1.1.1 把log大包（opentelemetry-exporter-ar-log-2.4.0-jar-with-dependencies.jar）用以下命令安装到maven仓库：【注意：-Dfile指定jar包的地址填写正确。】
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-ar-log-2.4.0-jar-with-dependencies.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-ar-log -Dversion=2.4.0 -Dpackaging=jar
###### 1.1.2 在pom.xml中引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>2.4.0</version>
</dependency>
```

#### 1.2 对于需要同时使用【ar-trace, ar-metrics, ar-log】且不可以连外网拉第三方库的情况：【优点: 离线环境下可以使用，比同时使用三个大包体积小】
###### 1.2.1 把common大包安装到maven仓库：【注意：-Dfile指定jar包的地址填写正确。】
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-common-2.4.0-jar-with-dependencies.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-common -Dversion=2.4.0 -Dpackaging=jar
###### 1.2.2 把ar-trace小包安装到maven仓库：
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-ar-trace-2.4.0.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-ar-trace -Dversion=2.4.0 -Dpackaging=jar
###### 1.2.3 把ar-metrics小包安装到maven仓库：
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-ar-metrics-2.4.0.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-ar-metrics -Dversion=2.4.0 -Dpackaging=jar
###### 1.2.4 把ar-log小包安装到maven仓库：
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-ar-log-2.4.0.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-ar-log -Dversion=2.4.0 -Dpackaging=jar
###### 1.2.5 在pom.xml中引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>2.4.0</version>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-trace</artifactId>
    <version>2.4.0</version>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-metrics</artifactId>
    <version>2.4.0</version>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>2.4.0</version>
</dependency>
```


#### 1.3  对于可以连外网拉第三方库的情况：【可以安装common小包和log小包，优点：体积小】
###### 1.3.1 把common大包安装到maven仓库：【注意：-Dfile指定jar包的地址填写正确。】
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-common-2.4.0.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-common -Dversion=2.4.0 -Dpackaging=jar
###### 1.3.2 把ar-log小包安装到maven仓库：
- mvn install:install-file -Dfile=D:/jar/opentelemetry-exporter-ar-log-2.4.0.jar -DgroupId=cn.aishu -DartifactId=opentelemetry-exporter-ar-log -Dversion=2.4.0 -Dpackaging=jar
###### 1.3.3 在pom.xml中引用：

```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>2.4.0</version>
</dependency>
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>2.4.0</version>
</dependency>
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.14</version>
</dependency>
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-core</artifactId>
    <version>5.8.14</version>
</dependency>
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk</artifactId>
    <version>1.23.1</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.30</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```


## 使用TelemetrySDK-Logger
    //1.字符串日志：
    public void testString(){
    Logger logger = LoggerFactory.getLogger(this.getClass());  //给个字符类型的日志名称，生成日志实例
    SamplerLogConfig.setLevel(Level.TRACE);                //（可选）配置系统日志等级，默认是INFO级别
    logger.trace("test");
    logger.debug("test");
    logger.info("test");                                        //生成info级别的字符串日志：test
    logger.warn("test");
    logger.error("test");
    logger.fatal("test");
    }
    //可以看见打印信息

    //2.在body添加自定义类日志：
    //测试用的自定义类:Animal
    private class Animal {
        private String name;
        private Integer age;

        public Animal() {
        }

        public Animal(String name, Integer age) {
            this.name = name;
            this.age = age;
        }

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(Integer age) {
            this.age = age;
        }
    }

    //2.1测试给Body添加Animal类的实例
    public void testBody() {
        final Logger logger = LoggerFactory.getLogger("this.getClass()");  //生成日志实例

        //Body: Animal实例
        final Animal animal = new Animal("little cat", 2);
        animal.setName("little cat");
        animal.setAge(2);

        final Body body = new Body();
        body.setType("animal");
        body.setField(animal);

        logger.info(body);
    }


    //2.2测试给Attributes添加Animal类的实例
    public void testAttributes() {
        final Logger logger = LoggerFactory.getLogger(this.getClass());  //生成日志实例

        //Attributes: Animal实例
        final Animal animal = new Animal("little cat", 2);

        Map<String, Object> attr =new HashMap<>();
        attr.put("animal1", animal);
        Attributes attributes = new Attributes(attr);

        logger.info("bodyAbc", attributes);
    }

    //3.1 用http或者https方式发送log，可以设置一些自定义配置
       public void testObjectHttpsSend() throws InterruptedException {
        //若要使用http发送log，需要以下设置，记得修改url地址
         SamplerLogConfig.setSender(HttpSender.create("http://10.4.130.68/api/feed_ingester/v1/jobs/job-090a252e12fc6960/events"));

        //若要使用https发送log，需要以下设置，记得修改url地址
        // SamplerLogConfig.setSender(HttpsSender.create("https://10.4.130.68/api/feed_ingester/v1/jobs/job-090a252e12fc6960/events"));

        final Logger logger = LoggerFactory.getLogger(this.getClass()); //生成日志实例

        //创建service
        Service service = new Service();
        service.setName("myServiceName");
        service.setInstanceId("myServiceInstanceId");
        service.setVersion("myServiceVersion2.4");

        //创建Attributes
        Map<String, Object> attr =new HashMap<>();
        attr.put("attr","test123str");
        Attributes attributes = new Attributes(attr);

        //创建link
        Link link = new Link();
        link.setTraceId("a64dfb055e90ccab9bbce30ab31040df");
        link.setSpanId("217400e1dbf690f9");

        //把刚刚自定义的各个配置添加到log：
        logger.info("str123testms", attributes, link, service);
    }

    //4.1可选择在resources添加配置文件slog.properties设置service和http.url或https.url,如：
        service.instance.id=myServiceInstanceId
        service.name=myServiceName
        service.version=myServiceVersion

        //http或者https，填一种方式就好，默认是打印在标准输出
        http.url=http://10.4.15.62/api/feed_ingester/v1/jobs/job-0e87b9ed98e52c30/events
        //或
        https.url=https://10.4.15.62/api/feed_ingester/v1/jobs/job-0e87b9ed98e52c30/events

    //5.1用logback方式发送log:
        SamplerLogConfig.setSender(LogbackSender.create());
        final Logger logger = LoggerFactory.getLogger(this.getClass());  //生成日志实例
        logger.info("test");                                        //生成info级别的字符串日志：test