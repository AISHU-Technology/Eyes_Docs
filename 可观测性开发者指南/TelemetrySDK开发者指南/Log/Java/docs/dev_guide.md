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
  opentelemetry-exporter-common-1.0.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-common-1.0.0-jar-with-dependencies.jar //大包，包含了所有依赖包
  opentelemetry-exporter-ar-log-1.0.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-ar-log-1.0.0-jar-with-dependencies.jar   //大包，包含了所有依赖包，包括opentelemetry-exporter-common
```

#### 1.1 最加单的使用方法：对于没有maven本地仓库的情况，把log大包（opentelemetry-exporter-ar-log-1.0.0-jar-with-dependencies.jar）
  放在与项目src同级目录，用以下方法引用。使用导入本地jar文件的方式引入包，这样可以在离线环境下使用
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>1.0.0</version>
    <type>jar</type>
    <scope>system</scope>
    <systemPath>${project.basedir}/opentelemetry-exporter-ar-trace-1.0.0-jar-with-dependencies.jar</systemPath>
</dependency>
```

#### 1.2  对于有maven仓库,并且可以连外网拉第三方库的情况：
- 把 opentelemetry-exporter-common-1.0.0.jar 和 opentelemetry-exporter-ar-log-1.0.0.jar 上传的maven仓库， 然后在pom.xml里引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>1.0.0</version>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>1.0.0</version>
</dependency>
```

#### 1.3  对于有maven仓库,但不可以连外网拉第三方库的情况：
- 把 opentelemetry-exporter-common-1.0.0-jar-with-dependencies.jar 和 opentelemetry-exporter-ar-log-1.0.0.jar 上传的maven仓库， 然后在pom.xml里引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>1.0.0</version>
    <classifier>jar-with-dependencies</classifier>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-log</artifactId>
    <version>1.0.0</version>
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

