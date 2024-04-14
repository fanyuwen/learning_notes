### 初始化Spring boot项目
+ maven  
  初始化`Spring boot`项目的时候,pom文件可以采用继承`spring-boot-starter-parent`的方式来使用,也可以在`dependencyManagement`标签里定义`spring-boot-dependencies`的依赖,例子如下:
  ```xml
     <!--<?xml version="1.0" encoding="UTF-8"?>-->
     <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.example</groupId>
        <artifactId>myproject</artifactId>
        <version>x.x.x-SNAPSHOT</version>

        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>x.x.xx</version>
        </parent>
        <!-- Additional lines to be added here... -->
    </project>
  ```
  **第二种方式有一个问题,就是不再允许覆盖相应的被管理的版本,如果要使用其他版本,需要在`spring-boot-dependencies`依赖管理上面添加一个完整的dependency**
  ```xml
     <!--<?xml version="1.0" encoding="UTF-8"?>-->
     <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.example</groupId>
        <artifactId>myproject</artifactId>
        <version>x.x.x-SNAPSHOT</version>
        <!-- Additional lines to be added here... -->
        <dependencyManagement>
            <dependencies>
                <dependency>
                    <!-- Import dependency management from Spring Boot -->
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-dependencies</artifactId>
                    <version>x.x.x</version>
                    <type>pom</type>
                    <scope>import</scope>
                </dependency>
            </dependencies>
        </dependencyManagement>
    </project>
  ```