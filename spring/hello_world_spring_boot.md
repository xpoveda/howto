
Hello world spring boot
========================

Información adicional
----------------------
https://spring.io/guides/gs/spring-boot/

https://github.com/xpoveda/gs-spring-boot

Bajandonos el proyecto de Github
--------------------------------
```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ git clone https://github.com/xpoveda/gs-spring-boot.git
```

Organización del proyecto
-------------------------
```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ tree
.
+-- build
¦   +-- classes
¦   ¦   +-- java
¦   ¦       +-- main
¦   ¦       ¦   +-- hello
¦   ¦       ¦       +-- Application.class
¦   ¦       ¦       +-- HelloController.class
¦   ¦       +-- test
¦   ¦           +-- hello
¦   ¦               +-- HelloControllerIT.class
¦   ¦               +-- HelloControllerTest.class
¦   +-- libs
¦   ¦   +-- gs-spring-boot-0.1.0.jar
¦   ¦   +-- gs-spring-boot-0.1.0.jar.original
¦   +-- reports
¦   ¦   +-- tests
¦   ¦       +-- test
¦   ¦           +-- classes
¦   ¦           ¦   +-- hello.HelloControllerIT.html
¦   ¦           ¦   +-- hello.HelloControllerTest.html
¦   ¦           +-- css
¦   ¦           ¦   +-- base-style.css
¦   ¦           ¦   +-- style.css
¦   ¦           +-- index.html
¦   ¦           +-- js
¦   ¦           ¦   +-- report.js
¦   ¦           +-- packages
¦   ¦               +-- hello.html
¦   +-- test-results
¦   ¦   +-- test
¦   ¦       +-- binary
¦   ¦       ¦   +-- output.bin
¦   ¦       ¦   +-- output.bin.idx
¦   ¦       ¦   +-- results.bin
¦   ¦       +-- TEST-hello.HelloControllerIT.xml
¦   ¦       +-- TEST-hello.HelloControllerTest.xml
¦   +-- tmp
¦       +-- compileJava
¦       +-- compileTestJava
¦       +-- jar
¦           +-- MANIFEST.MF
+-- build.gradle
+-- gradle
¦   +-- wrapper
¦       +-- gradle-wrapper.jar
¦       +-- gradle-wrapper.properties
+-- gradlew
+-- gradlew.bat
+-- mvnw
+-- mvnw.cmd
+-- pom.xml
+-- src
    +-- main
    ¦   +-- java
    ¦       +-- hello
    ¦           +-- Application.java
    ¦           +-- HelloController.java
    +-- test
        +-- java
            +-- hello
                +-- HelloControllerIT.java
                +-- HelloControllerTest.java
```

Generación con Maven
---------------------
```xml
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-boot</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.7.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- tag::actuator[] -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!-- end::actuator[] -->
        <!-- tag::tests[] -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- end::tests[] -->
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <artifactId>maven-failsafe-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>integration-test</goal>
                            <goal>verify</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>
```

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ mvn clean 
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ mvn package

ubuntu@ubuntu:~$ find . -name *gs-spring-boot*
./misproyectos/gs-spring-boot
./misproyectos/gs-spring-boot/complete/target/gs-spring-boot-0.1.0.jar
./misproyectos/gs-spring-boot/complete/target/gs-spring-boot-0.1.0.jar.original

ubuntu@ubuntu:~$ ls -lt ./misproyectos/gs-spring-boot/complete/target/gs-spring-boot-0.1.0.jar
-rw-rw-r-- 1 ubuntu ubuntu 15022674 Sep 18 14:39 ./misproyectos/gs-spring-boot/complete/target/gs-spring-boot-0.1.0.jar
```

Y ejecutamos el servidor
```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ java -jar target/gs-spring-boot-0.1.0.jar
```

Y lanzamos el cliente por `navegador` o con `lynx` o `curl`
```bash
ubuntu@ubuntu:~$ curl http://localhost:8080
Greetings from Spring Boot!
```

Generación con Gradle
---------------------
```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more build.gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.5.7.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'

jar {
    baseName = 'gs-spring-boot'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    // tag::actuator[]
    compile("org.springframework.boot:spring-boot-starter-actuator")
    // end::actuator[]
    // tag::tests[]
    testCompile("org.springframework.boot:spring-boot-starter-test")
    // end::tests[]
}
```

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ gradle clean
Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 15s
1 actionable task: 1 up-to-date
```

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ gradle build

> Task :test
2017-09-18 14:48:23.040  INFO 66810 --- [       Thread-9] ationConfigEmbeddedWebApplicationContext : Closing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@17200487: startup date [Mon Sep 18 14:48:20 PDT 2017]; root of context hierarchy
2017-09-18 14:48:23.048  INFO 66810 --- [       Thread-6] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@6ac09e1: startup date [Mon Sep 18 14:48:14 PDT 2017]; root of context hierarchy


BUILD SUCCESSFUL in 28s
6 actionable tasks: 6 executed
```

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ find . -name "*.java"
./src/test/java/hello/HelloControllerTest.java
./src/test/java/hello/HelloControllerIT.java
./src/main/java/hello/Application.java
./src/main/java/hello/HelloController.java
```

```java
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more ./src/test/java/hello/HelloControllerTest.java
package hello;

import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {

    @Autowired
    private MockMvc mvc;

    @Test
    public void getHello() throws Exception {
        mvc.perform(MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string(equalTo("Greetings from Spring Boot!")));
    }
}
```

```java 
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more ./src/main/java/hello/HelloController.java
package hello;

import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
public class HelloController {

    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }

}
```

```java
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more ./src/main/java/hello/Application.java
package hello;

import java.util.Arrays;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
        return args -> {

            System.out.println("Let's inspect the beans provided by Spring Boot:");

            String[] beanNames = ctx.getBeanDefinitionNames();
            Arrays.sort(beanNames);
            for (String beanName : beanNames) {
                System.out.println(beanName);
            }

        };
    }

}
```

```java
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more ./src/test/java/hello/HelloControllerIT.java
package hello;

import static org.hamcrest.Matchers.equalTo;
import static org.junit.Assert.assertThat;

import java.net.URL;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.context.embedded.LocalServerPort;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIT {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void getHello() throws Exception {
        ResponseEntity<String> response = template.getForEntity(base.toString(),
                String.class);
        assertThat(response.getBody(), equalTo("Greetings from Spring Boot!"));
    }
}
```
