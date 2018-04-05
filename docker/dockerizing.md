
Creacion, subida y descarga de imagenes docker
==============================================

Subida de imagenes docker ya existentes en local
------------------------------------------------

Tenemos solo una imagen bajada de docker, el `hello world` de la primera instalacion de docker.
```
root@ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              f2a91732366c        4 months ago        1.85kB
```
Sobre él podemos aplicarle un `tag`, que necesariamente tendrá que empezar por nuestro usuario de `Docker hub`.
Tambien podemos añadirle mas etiquetas como versionados y demas con ":", por ejemplo `xpoveda/hello-world:v1`.
```
root@ubuntu:~# docker tag f2a91732366c xpoveda/hello-world

root@ubuntu:~# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
hello-world           latest              f2a91732366c        4 months ago        1.85kB
xpoveda/hello-world   latest              f2a91732366c        4 months ago        1.85kB
```
Para poder hacer un `push`, es decir subir la imagen al registro de `Docker hub` lo que haremos antes de nada es hacer un `docker login`
```
root@ubuntu:~# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xpoveda
Password:
Login Succeeded
```
Y seguidamente el `docker push`
```
root@ubuntu:~# docker push xpoveda/hello-world
The push refers to repository [docker.io/xpoveda/hello-world]
f999ae22f308: Pushed
latest: digest: sha256:8072a54ebb3bc136150e2f2860f00a7bf45f13eeb917cca2430fcd0054c8e51b size: 524
```
Así tendremos la entrada en https://hub.docker.com/r/xpoveda/hello-world/

Descarga de imagenes docker
---------------------------

Partimos de una situación 0 en nuestro local ejecutando este comando
```
docker rmi -f $(docker images -q)

root@ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
Con un `pull` descargamos la imagen del registro de Docker a nuestro local.
```
root@ubuntu:~# docker pull xpoveda/hello-world
Using default tag: latest
latest: Pulling from xpoveda/hello-world
ca4f61b1923c: Already exists
Digest: sha256:8072a54ebb3bc136150e2f2860f00a7bf45f13eeb917cca2430fcd0054c8e51b
Status: Downloaded newer image for xpoveda/hello-world:latest

root@ubuntu:~# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
xpoveda/hello-world   latest              f2a91732366c        4 months ago        1.85kB
```
Y lo ejecutamos con `docker run`
```
root@ubuntu:~# docker run xpoveda/hello-world

Hello from Docker!
```

Descarga de proyecto springboot y ejecucion normal
--------------------------------------------------

Nos bajamos el proyecto inicial de springboot https://spring.io/guides/gs/spring-boot-docker/ con un `git clone`
```
git clone https://github.com/spring-guides/gs-spring-boot-docker.git

root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# tree
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

Hacemos un `mvn clean` y despues un `mvn install` o `mvn package` para generar el jar final (lo que queramos) y ejecutar el servidor.
```
root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# mvn install
root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# ./mvnw package && java -jar target/gs-spring-boot-docker-0.1.0.jar
```
Y despues ejecutamos el cliente en otra maquina. Se hace notar que ha de estar activado el `export no_proxy=localhost` en el `/etc/profile` para que las llamadas a localhost desde ssh no vayan por el proxy.
```
root@ubuntu:~# curl http://localhost:8080
Hello Docker World
```

Ejecucion con docker
--------------------
Realizamos una pequeña modificacion en el `Dockerfile` para que funcione ya que hay un problema con los argumentos de la linea "target" si la ruta no es absoluta sino referenciada por "args". Esta es la version correcta:

```
root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# more Dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD target/*.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

Y a continuacion hacemos un `docker build` y vemos que se añaden las imagenes a nuestro sistema.
```
root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# docker build -f Dockerfile -t xpoveda/gs-spring-boot-docker .
Sending build context to Docker daemon  32.37MB
Step 1/4 : FROM openjdk:8-jdk-alpine
8-jdk-alpine: Pulling from library/openjdk
ff3a5c916c92: Already exists
5de5f69f42d7: Already exists
fd869c8b9b59: Already exists
Digest: sha256:4cd17a64b67df1a929a9c6dedf513afcdc48f3ca0b7fddee6489d0246a14390b
Status: Downloaded newer image for openjdk:8-jdk-alpine
 ---> 224765a6bdbe
Step 2/4 : VOLUME /tmp
 ---> Running in ee10c9548ed9
Removing intermediate container ee10c9548ed9
 ---> 923a5512c48e
Step 3/4 : ADD target/*.jar app.jar
 ---> df857e9ea4e0
Step 4/4 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
 ---> Running in 3f672a2edcd1
Removing intermediate container 3f672a2edcd1
 ---> 776fb89d5fd1
Successfully built 776fb89d5fd1
Successfully tagged xpoveda/gs-spring-boot-docker:latest

root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
xpoveda/gs-spring-boot-docker   latest              776fb89d5fd1        7 seconds ago       118MB
openjdk                         8-jdk-alpine        224765a6bdbe        2 months ago        102MB
```

Ejecutamos y vemos que funciona correctamente
```
root@ubuntu:/home/ubuntu/misproyectos/gs-spring-boot-docker/complete# docker run -p 8080:8080 xpoveda/gs-spring-boot-docker

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.0.RELEASE)

2018-04-05 10:57:00.764  INFO 1 --- [           main] hello.Application                        : Starting Application v0.1.0 on 60a837f00181 with PID 1 (/app.jar started by root in /)
2018-04-05 10:57:00.808  INFO 1 --- [           main] hello.Application                        : No active profile set, falling back to default profiles: default
2018-04-05 10:57:01.366  INFO 1 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@c818063: startup date [Thu Apr 05 10:57:01 GMT 2018]; root of context hierarchy
2018-04-05 10:57:10.154  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2018-04-05 10:57:10.357  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2018-04-05 10:57:10.359  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.28
2018-04-05 10:57:10.454  INFO 1 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server:/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64:/usr/lib/jvm/java-1.8-openjdk/jre/../lib/amd64:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
2018-04-05 10:57:10.987  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2018-04-05 10:57:10.988  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 9667 ms
2018-04-05 10:57:11.774  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2018-04-05 10:57:11.792  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2018-04-05 10:57:11.795  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2018-04-05 10:57:11.798  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2018-04-05 10:57:11.801  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2018-04-05 10:57:13.869  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@c818063: startup date [Thu Apr 05 10:57:01 GMT 2018]; root of context hierarchy
2018-04-05 10:57:14.402  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String hello.Application.home()
2018-04-05 10:57:14.453  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2018-04-05 10:57:14.455  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2018-04-05 10:57:14.690  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-04-05 10:57:14.701  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-04-05 10:57:14.931  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-04-05 10:57:15.773  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-04-05 10:57:16.270  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2018-04-05 10:57:16.288  INFO 1 --- [           main] hello.Application                        : Started Application in 19.334 seconds (JVM running for 21.926)
```

```
root@ubuntu:~# curl http://localhost:8080
Hello Docker World
```

El versionado como `v1` y la subida de la nueva imagen lo podemos hacer así:
```
root@ubuntu:~# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
xpoveda/gs-spring-boot-docker   latest              776fb89d5fd1        2 minutes ago       118MB
openjdk                         8-jdk-alpine        224765a6bdbe        2 months ago        102MB


root@ubuntu:~# docker tag 776fb89d5fd1 xpoveda/gs-spring-boot-docker:v1


root@ubuntu:~# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
xpoveda/gs-spring-boot-docker   latest              776fb89d5fd1        3 minutes ago       118MB
xpoveda/gs-spring-boot-docker   v1                  776fb89d5fd1        3 minutes ago       118MB
openjdk                         8-jdk-alpine        224765a6bdbe        2 months ago        102MB


root@ubuntu:~# docker push xpoveda/gs-spring-boot-docker:v1
The push refers to repository [docker.io/xpoveda/gs-spring-boot-docker]
94f269063042: Pushed
685fdd7e6770: Mounted from library/openjdk
c9b26f41504c: Mounted from library/openjdk
cd7100a72410: Mounted from library/openjdk
v1: digest: sha256:4e24a7e1d1c2db1e495f7d18e8862921bbdcd83fcf133a1de49ab52909a9d045 size: 1159
```

Volvemos a dejar el sistema sin imagenes
```
root@ubuntu:~# docker rmi -f $(docker images -q)

root@ubuntu:~# docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                    NAMES
60a837f00181        xpoveda/gs-spring-boot-docker   "java -Djava.securit…"   8 minutes ago       Up 8 minutes        0.0.0.0:8080->8080/tcp   trusting_dubinsky

root@ubuntu:~# docker kill 60a837f00181
60a837f00181

root@ubuntu:~# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Y nos descargamos la ultima version
```
root@ubuntu:~# docker pull xpoveda/gs-spring-boot-docker:v1
v1: Pulling from xpoveda/gs-spring-boot-docker
ff3a5c916c92: Already exists
5de5f69f42d7: Already exists
fd869c8b9b59: Already exists
89d5ec815ba4: Already exists
Digest: sha256:4e24a7e1d1c2db1e495f7d18e8862921bbdcd83fcf133a1de49ab52909a9d045
Status: Downloaded newer image for xpoveda/gs-spring-boot-docker:v1

root@ubuntu:~# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
xpoveda/gs-spring-boot-docker   v1                  776fb89d5fd1        12 minutes ago      118MB
```

Ejecutandola sin problema con `docker run -p 8080:8080 xpoveda/gs-spring-boot-docker:v1`

Todas las versiones que añadamos de nuestro repositorio se guardaran en `https://hub.docker.com/r/xpoveda/gs-spring-boot-docker/`

Para borrar alguna de las versiones tenemos un icono de papelera en la seccion `tags` y para que el repositorio sea privado o lo borremos por completo iremos a `settings`.


Más documentacion
-----------------
* https://medium.com/@itsromiljain/docker-setup-and-dockerize-an-application-5c24a4c8b428
* https://medium.com/@itsromiljain/dockerize-rest-spring-boot-application-with-hibernate-having-database-as-mysql-579abcc4edc4
* https://medium.com/@itsromiljain/docker-compose-file-to-run-rest-spring-boot-application-container-and-mysql-container-775f15d21416



