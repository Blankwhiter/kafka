

写在前面：本文将使用的是kafka单节点docker环境 
读者如果想在Windows安装运行Kafka环境，请参考 https://www.cnblogs.com/flower1990/p/7466882.html

代码地址： https://github.com/Blankwhiter/kafka

# 第一步 搭建kafka环境
 参考教程 https://blog.csdn.net/belonghuang157405/article/details/82149257

# 第二步 springboot集成kafka
1.引入kafka包
**pom.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>kafka</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>kafka</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--引入kafka-->
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>


</project>

```
2.编写kafka配置
**application.yml**
```xml
spring:
  kafka:
    bootstrap-servers: 192.168.9.219:9092
    consumer:
      group-id: consumer
      enable-auto-commit: true
      auto-commit-interval: 1000s

```
3.编写消息消费者
**kafkaConsumer.java**
```java
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class KafkaConsumer {

    /**
     * 接收kafka消息 可接收多个topic消息
     * @param message
     */
    @KafkaListener(topics = {"test1"})
    public void receiveMessage(String message){
        System.out.println("--------开始接收消息--------");
        System.out.println("        接收消息 ："+message);
        System.out.println("--------结束接收消息--------");
    }
}

```
4.编写消息消费者
**MessageController.java**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaAdmin;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MessageController {

    /**
     * 配置kafka系统组件
     */
    @Autowired
    KafkaAdmin kafkaAdmin;

    /**
     * kafkaTemplate 发送消息Template
     */
    @Autowired
    KafkaTemplate kafkaTemplate;

    /**
     * 发送消息
     */
    @RequestMapping("/send")
    public String  sendMessage(){
        kafkaTemplate.send("test1","test - msg");
        return "success";
    }

}

```
5.启动项目，并再浏览器访问 http://localhost:8080/send
即可在控制台看到打印出的消息
```
------------开始接收消息----------
            接收消息 ：test - msg
------------结束接收消息----------
```
