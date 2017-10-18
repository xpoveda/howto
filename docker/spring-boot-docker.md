Spring boot Helloworld en docker
================================

## Revisamos versiones

Primero de todo comprobamos las versiones que tenemos de java, maven y gradle.
```
root@ubuntu:~/misproyectos# java -version
openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2ubuntu1.16.04.3-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)

root@ubuntu:~/misproyectos# mvn -version
Apache Maven 3.3.9
Maven home: /usr/share/maven
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.10.0-35-generic", arch: "amd64", family: "unix"

root@ubuntu:~/misproyectos# gradle -version

------------------------------------------------------------
Gradle 4.1
------------------------------------------------------------

Build time:   2017-08-07 14:38:48 UTC
Revision:     941559e020f6c357ebb08d5c67acdb858a3defc2

Groovy:       2.4.11
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_131 (Oracle Corporation 25.131-b11)
OS:           Linux 4.10.0-35-generic amd64
```

## Instalacion

Nos bajamos el paquete que se indica en la url https://spring.io/guides/gs/spring-boot-docker/
```
root@ubuntu:~/misproyectos# git clone https://github.com/spring-guides/gs-spring-boot-docker.git
Cloning into 'gs-spring-boot-docker'...
remote: Counting objects: 564, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 564 (delta 5), reused 10 (delta 4), pack-reused 550
Receiving objects: 100% (564/564), 264.47 KiB | 383.00 KiB/s, done.
Resolving deltas: 100% (347/347), done.
Checking connectivity... done.
```

Comprobamos que aspecto tendra
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# tree
.
├── build.gradle
├── Dockerfile
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── hello
    │   │       └── Application.java
    │   └── resources
    │       └── application.yml
    └── test
        └── java
            └── hello
                └── HelloWorldConfigurationTests.java
```

Asi como el codigo fuente de la clase principal
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# more ./src/main/java/hello/Application.java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class Application {

    @RequestMapping("/")
    public String home() {
        return "Hello Docker World";
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```
## Generacion con GRADLE

Este es el fichero de build
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# more build.gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath('org.springframework.boot:spring-boot-gradle-plugin:1.5.8.RELEASE')
// tag::build[]
        classpath('se.transmode.gradle:gradle-docker:1.2')
// end::build[]
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
// tag::plugin[]
apply plugin: 'docker'
// end::plugin[]

// This is used as the docker image prefix (org)
group = 'springio'

jar {
    baseName = 'gs-spring-boot-docker'
    version =  '0.1.0'
}

// tag::task[]
task buildDocker(type: Docker, dependsOn: build) {
  applicationName = jar.baseName
  dockerfile = file('Dockerfile')
  doFirst {
    copy {
      from jar
      into "${stageDir}/target"
    }
  }
}
// end::task[]

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

Y ejecutamos gradle
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# ./gradlew build buildDocker
:compileJava
:processResources
:classes
:findMainClass
:jar
:bootRepackage
:assemble
:compileTestJava
:processTestResources UP-TO-DATE
:testClasses
:test
:check
:build
:buildDocker
Sending build context to Docker daemon  14.49MB
Step 1/5 : FROM openjdk:8-jdk-alpine
8-jdk-alpine: Pulling from library/openjdk
88286f41530e: Pulling fs layer
720349d0916a: Pulling fs layer
42a4b3080d3c: Pulling fs layer
720349d0916a: Verifying Checksum
720349d0916a: Download complete
88286f41530e: Verifying Checksum
88286f41530e: Download complete
88286f41530e: Pull complete
720349d0916a: Pull complete
42a4b3080d3c: Verifying Checksum
42a4b3080d3c: Download complete
42a4b3080d3c: Pull complete
Digest: sha256:1c6a5d2aee2a9cb6c5463c54ba7877f5b4cfb75c86dfdc7b338fe952254e4707
Status: Downloaded newer image for openjdk:8-jdk-alpine
 ---> a8bd10541772
Step 2/5 : VOLUME /tmp
 ---> Running in 68e3378d7feb
 ---> cc8846497c3e
Removing intermediate container 68e3378d7feb
Step 3/5 : ADD target/gs-spring-boot-docker-0.1.0.jar app.jar
 ---> 0750dc0a27f9
Removing intermediate container 89ad5dbdfda6
Step 4/5 : ENV JAVA_OPTS ""
 ---> Running in c2944df20b41
 ---> 941e8330b491
Removing intermediate container c2944df20b41
Step 5/5 : ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar
 ---> Running in 2cd259fc42f3
 ---> 440e33e9720e
Removing intermediate container 2cd259fc42f3
Successfully built 440e33e9720e
Successfully tagged springio/gs-spring-boot-docker:latest


BUILD SUCCESSFUL

Total time: 1 mins 0.692 secs

This build could be faster, please consider using the Gradle Daemon: https://docs.gradle.org/2.13/userguide/gradle_daemon.html
```

El cual nos ha creado la imagen y la ha desplegado en docker
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
springio/gs-spring-boot-docker   latest              440e33e9720e        35 seconds ago      116MB
openjdk                          8-jdk-alpine        a8bd10541772        4 weeks ago         101MB
```

## Ejecucion

La ejecutamos
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# docker run -p 8080:8080 -t springio/gs-spring-boot-docker

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.8.RELEASE)

2017-10-18 22:08:57.625  INFO 1 --- [           main] hello.Application                        : Starting Application on 84c4cc315bd6 with PID 1 (/app.jar started by root in /)
2017-10-18 22:08:57.637  INFO 1 --- [           main] hello.Application                        : No active profile set, falling back to default profiles: default
2017-10-18 22:08:57.835  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@3d82c5f3: startup date [Wed Oct 18 22:08:57 GMT 2017]; root of context hierarchy
2017-10-18 22:09:01.074  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2017-10-18 22:09:01.116  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2017-10-18 22:09:01.123  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.23
2017-10-18 22:09:01.347  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2017-10-18 22:09:01.348  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 3523 ms
2017-10-18 22:09:01.672  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Mapping servlet: 'dispatcherServlet' to [/]
2017-10-18 22:09:01.677  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2017-10-18 22:09:01.684  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2017-10-18 22:09:01.684  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2017-10-18 22:09:01.684  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2017-10-18 22:09:02.461  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@3d82c5f3: startup date [Wed Oct 18 22:08:57 GMT 2017]; root of context hierarchy
2017-10-18 22:09:02.632  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String hello.Application.home()
2017-10-18 22:09:02.636  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2017-10-18 22:09:02.642  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2017-10-18 22:09:02.727  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:09:02.735  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:09:02.821  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:09:03.124  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-10-18 22:09:03.254  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-10-18 22:09:03.265  INFO 1 --- [           main] hello.Application                        : Started Application in 6.673 seconds (JVM running for 7.525)
```

Y funciona
```
ubuntu@ubuntu:~$ curl -s http://localhost:8080
Hello Docker World
```
## Generación con MAVEN

Tambien lo podemos crear con maven.

```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# more pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-boot-docker</artifactId>
    <version>0.1.0</version>
    <packaging>jar</packaging>
    <name>Spring Boot Docker</name>
    <description>Getting started with Spring Boot and Docker</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.8.RELEASE</version>
        <relativePath />
    </parent>

    <properties>
        <docker.image.prefix>springio</docker.image.prefix>
        <java.version>1.8</java.version>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!-- tag::plugin[] -->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.3.4</version>
                <configuration>
                    <repository>${docker.image.prefix}/${project.artifactId}</repository>
                </configuration>
            </plugin>
            <!-- end::plugin[] -->

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>unpack</id>
                        <phase>package</phase>
                        <goals>
                            <goal>unpack</goal>
                        </goals>
                        <configuration>
                            <artifactItems>
                                <artifactItem>
                                    <groupId>${project.groupId}</groupId>
                                    <artifactId>${project.artifactId}</artifactId>
                                    <version>${project.version}</version>
                                </artifactItem>
                            </artifactItems>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# ./mvnw install dockerfile:build
```

```
[INFO]
[INFO] --- maven-jar-plugin:2.6:jar (default-jar) @ gs-spring-boot-docker ---
[INFO] Building jar: /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/target/gs-spring-boot-docker-0.1.0.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.5.8.RELEASE:repackage (default) @ gs-spring-boot-docker ---
[INFO]
[INFO] --- maven-dependency-plugin:2.10:unpack (unpack) @ gs-spring-boot-docker ---
[INFO] Configured Artifact: org.springframework:gs-spring-boot-docker:0.1.0:jar
[INFO] Unpacking /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/target/gs-spring-boot-docker-0.1.0.jar to /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/target/dependency with includes "" and excludes ""
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ gs-spring-boot-docker ---
[INFO] Installing /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/target/gs-spring-boot-docker-0.1.0.jar to /root/.m2/repository/org/springframework/gs-spring-boot-docker/0.1.0/gs-spring-boot-docker-0.1.0.jar
[INFO] Installing /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/pom.xml to /root/.m2/repository/org/springframework/gs-spring-boot-docker/0.1.0/gs-spring-boot-docker-0.1.0.pom
[INFO]
[INFO] --- dockerfile-maven-plugin:1.3.4:build (default-cli) @ gs-spring-boot-docker ---
[INFO] Building Docker context /home/ubuntu/misproyectos/gs-spring-boot-docker/complete
[INFO]
[INFO] Image will be built as springio/gs-spring-boot-docker:latest
[INFO]
[INFO] Step 1/5 : FROM openjdk:8-jdk-alpine
[INFO] Pulling from library/openjdk
[INFO] Image 88286f41530e: Pulling fs layer
[INFO] Image 720349d0916a: Pulling fs layer
[INFO] Image 42a4b3080d3c: Pulling fs layer
[INFO] Image 88286f41530e: Downloading
[INFO] Image 720349d0916a: Downloading
[INFO] Image 720349d0916a: Verifying Checksum
[INFO] Image 720349d0916a: Download complete
[INFO] Image 88286f41530e: Verifying Checksum
[INFO] Image 88286f41530e: Download complete
[INFO] Image 88286f41530e: Extracting
[INFO] Image 88286f41530e: Pull complete
[INFO] Image 720349d0916a: Extracting
[INFO] Image 42a4b3080d3c: Downloading
[INFO] Image 720349d0916a: Pull complete
[INFO] Image 42a4b3080d3c: Verifying Checksum
[INFO] Image 42a4b3080d3c: Download complete
[INFO] Image 42a4b3080d3c: Extracting
[INFO] Image 42a4b3080d3c: Pull complete
[INFO] Digest: sha256:1c6a5d2aee2a9cb6c5463c54ba7877f5b4cfb75c86dfdc7b338fe952254e4707
[INFO] Status: Downloaded newer image for openjdk:8-jdk-alpine
[INFO]  ---> a8bd10541772
[INFO] Step 2/5 : VOLUME /tmp
[INFO]  ---> Running in 33f021ecd678
[INFO]  ---> 1c56cd5596c5
[INFO] Removing intermediate container 33f021ecd678
[INFO] Step 3/5 : ADD target/gs-spring-boot-docker-0.1.0.jar app.jar
[INFO]  ---> 4919592cfba7
[INFO] Removing intermediate container f7b8fd95a168
[INFO] Step 4/5 : ENV JAVA_OPTS ""
[INFO]  ---> Running in 11f8b5f93df5
[INFO]  ---> ee7adf9f1910
[INFO] Removing intermediate container 11f8b5f93df5
[INFO] Step 5/5 : ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar
[INFO]  ---> Running in 907a62bca523
[INFO]  ---> 2755dc78bf5f
[INFO] Removing intermediate container 907a62bca523
[INFO] Successfully built 2755dc78bf5f
[INFO] Successfully tagged springio/gs-spring-boot-docker:latest
[INFO]
[INFO] Detected build of image with id 2755dc78bf5f
[INFO] Building jar: /home/ubuntu/misproyectos/gs-spring-boot-docker/complete/target/gs-spring-boot-docker-0.1.0-docker-info.jar
[INFO] Successfully built springio/gs-spring-boot-docker:latest
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 56.064 s
[INFO] Finished at: 2017-10-18T15:28:15-07:00
[INFO] Final Memory: 33M/79M
[INFO] ------------------------------------------------------------------------
```
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
springio/gs-spring-boot-docker   latest              2755dc78bf5f        53 seconds ago      116MB
openjdk                          8-jdk-alpine        a8bd10541772        4 weeks ago         101MB
```

Lo ejecutamos 
```
root@ubuntu:~/misproyectos/gs-spring-boot-docker/complete# docker run -e "SPRING_PROFILES_ACTIVE=prod" -p 8080:8080 -t springio/gs-spring-boot-docker

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.8.RELEASE)

2017-10-18 22:29:55.998  INFO 1 --- [           main] hello.Application                        : Starting Application v0.1.0 on 7ff362cba16f with PID 1 (/app.jar started by root in /)
2017-10-18 22:29:56.016  INFO 1 --- [           main] hello.Application                        : The following profiles are active: prod
2017-10-18 22:29:56.213  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@1b9e1916: startup date [Wed Oct 18 22:29:56 GMT 2017]; root of context hierarchy
2017-10-18 22:29:59.513  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2017-10-18 22:29:59.560  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2017-10-18 22:29:59.568  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.23
2017-10-18 22:29:59.761  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2017-10-18 22:29:59.761  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 3553 ms
2017-10-18 22:30:00.072  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Mapping servlet: 'dispatcherServlet' to [/]
2017-10-18 22:30:00.076  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2017-10-18 22:30:00.078  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2017-10-18 22:30:00.079  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2017-10-18 22:30:00.080  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2017-10-18 22:30:00.795  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@1b9e1916: startup date [Wed Oct 18 22:29:56 GMT 2017]; root of context hierarchy
2017-10-18 22:30:00.967  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String hello.Application.home()
2017-10-18 22:30:00.975  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2017-10-18 22:30:00.976  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2017-10-18 22:30:01.053  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:30:01.053  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:30:01.132  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-18 22:30:01.416  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-10-18 22:30:01.545  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-10-18 22:30:01.556  INFO 1 --- [           main] hello.Application                        : Started Application in 6.59 seconds (JVM running for 7.482)
2017-10-18 22:30:10.387  INFO 1 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2017-10-18 22:30:10.387  INFO 1 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
2017-10-18 22:30:10.433  INFO 1 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 46 ms
```

Y tambien funciona
```
ubuntu@ubuntu:~/misproyectos/gs-spring-boot-docker/complete$ curl -s http://localhost:8080
Hello Docker World
```
