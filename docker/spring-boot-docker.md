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

