---
title: "Springboot整合Nacos"
date: 2022-08-25T00:47:02+08:00
draft: true
---
安装Nacos服务器，运行startup.cmd，登录Nocas，用户名和密码默认都是root。
![Alt](/images/nacosLoginPage.png)
IDEA在父工程中加入
```sh
    <properties>
        <spring-boot.version>2.3.2.RELEASE</spring-boot.version>
        <spring-cloud.version>Hoxton.SR9</spring-cloud.version>
        <spring-cloud-alibaba.version>2.2.6.RELEASE</spring-cloud-alibaba.version>
    </properties>

    <!--  只声明依赖，不引入依赖 -->
    <dependencyManagement>
        <dependencies>
            <!-- 声明springBoot版本 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- 声明springCloud版本 -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- 声明 springCloud Alibaba 版本 -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
在子项目中，引入
```sh
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```
创建bootstrap创建bootstrap.properties配置文件，内容如下：
```sh    
spring.application.name=test
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
spring.cloud.nacos.config.namespace=440aaa71-72dd-4cb5-8aec-0bd634958a82
spring.cloud.nacos.config.extension-configs[0].data-id=mysql_common_properties
spring.cloud.nacos.config.extension-configs[0].group=DEFAULT_GROUP
spring.cloud.nacos.config.extension-configs[0].refresh=true
spring.cloud.nacos.config.extension-configs[1].data-id=redis_common_properties
spring.cloud.nacos.config.extension-configs[1].group=DEFAULT_GROUP
spring.cloud.nacos.config.extension-configs[1].refresh=true
```
编写一个Controller进行读取配置测试，使用Environment的getProperty方法读取配置信息，若Nacos修改配置信息，客户端可以观察到修改后的结果
```sh
@RestController
@RequestMapping("/nacos")
public class NacosConfigController {
	@Autowired
    Environment environment;
	@RequestMapping("/getConfig3")
    public String getConfig3(){
        String mysql = environment.getProperty("mysql");
        String redis = environment.getProperty("redis");
        return mysql + "  " + redis;
    }
}
```
如果使用@Value注解，需要加入@RefreshScope注解，才能获取修改后的配置
```sh
@RestController
@RequestMapping("/nacos")
@RefreshScope
public class NacosConfigController {
    @Value("${agnes.name}")
    private String name;
    @Value("${agnes.age}")
    private String age;
    @GetMapping("/getConfig")
    public String getConfig(){
        return name + ":" + age;
    }
}
```
未完待续



