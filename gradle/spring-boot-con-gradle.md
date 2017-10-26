
En un entorno con proxy levantado tendremos que habilitar los datos para poderselo saltar.
En el apartado "proxy" de esta documentación se indica como.

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ vi gradle.properties
```

El fichero de configuracion 'build.gradle' es donde indicaremos el repositorio donde extraeremos las librerias, cuales de ellas y el jar
final que se generará.

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more build.gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.5.8.RELEASE")
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

Hacemos un clean para borrar todo atisbo de fichero generado y despues build para generar el jar.

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ gradle clean build
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gradle-plugin/1.5.8.RELEASE/spring-boot-gradle-plugin-1.5.8.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-gradle-plugin/1.5.8.RELEASE/spring-boot-gradle-plugin-1.5.8.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-actuator/1.5.8.RELEASE/spring-boot-starter-actuator-1.5.8.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-actuator/1.5.8.RELEASE/spring-boot-actuator-1.5.8.RELEASE.pom
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-actuator/1.5.8.RELEASE/spring-boot-starter-actuator-1.5.8.RELEASE.jar
Download https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-actuator/1.5.8.RELEASE/spring-boot-actuator-1.5.8.RELEASE.jar

> Task :test
2017-10-26 10:53:28.872  INFO 35264 --- [       Thread-6] ationConfigEmbeddedWebApplicationContext : Closing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@101b2c31: startup date [Thu Oct 26 10:53:08 PDT 2017]; root of context hierarchy
2017-10-26 10:53:28.875  INFO 35264 --- [       Thread-9] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@6ca7fad3: startup date [Thu Oct 26 10:53:24 PDT 2017]; root of context hierarchy


BUILD SUCCESSFUL in 1m 17s
7 actionable tasks: 6 executed, 1 up-to-date
```

Comprobamos que hay.

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ find . -name "*.jar"
./gradle/wrapper/gradle-wrapper.jar
./.mvn/wrapper/maven-wrapper.jar
./build/libs/gs-spring-boot-0.1.0.jar
```

Y finalmente lo ejecutamos.

```bash
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ java -jar build/libs/gs-spring-boot-0.1.0.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.8.RELEASE)

2017-10-26 11:00:17.265  INFO 37813 --- [           main] hello.Application                        : Starting Application on ubuntu with PID 37813 (/home/ubuntu/misproyectos/gs-spring-boot/complete/build/libs/gs-spring-boot-0.1.0.jar started by ubuntu in /home/ubuntu/misproyectos/gs-spring-boot/complete)
2017-10-26 11:00:17.282  INFO 37813 --- [           main] hello.Application                        : No active profile set, falling back to default profiles: default
2017-10-26 11:00:17.537  INFO 37813 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@4459eb14: startup date [Thu Oct 26 11:00:17 PDT 2017]; root of context hierarchy
```

Et voilà
```bash
ubuntu@ubuntu:~$ curl http://localhost:8080
Greetings from Spring Boot!
```
