Trabajando con spring-cloud-examples
====================================
Nos descargamos el repositorio
```
git clone https://github.com/ityouknow/spring-cloud-examples
```
Instalamos uno de ellos 
```
ubuntu@ubuntu:~/proyectos/spring-cloud-examples/spring-cloud-sleuth-zipkin$ mvn install
```
Y buscamos los jar para ver que elementos ejecutar con `java -jar`
```
ubuntu@ubuntu:~/proyectos/spring-cloud-examples/spring-cloud-sleuth-zipkin$ find . -name "*jar"
./zipkin-server/target/zipkin-server-1.0.0.BUILD-SNAPSHOT.jar
./spring-cloud-eureka/target/spring-cloud-eureka-1.0.0.BUILD-SNAPSHOT.jar
./spring-cloud-zuul/target/spring-cloud-zuul-1.0.0.BUILD-SNAPSHOT.jar
./spring-cloud-producer/target/spring-cloud-producer-1.0.0.BUILD-SNAPSHOT.jar

ubuntu@ubuntu:~/proyectos/spring-cloud-examples/spring-cloud-sleuth-zipkin$ java -jar ./spring-cloud-zuul/target/spring-cloud-zuul-1.0.0.BUILD-SNAPSHOT.jar
```
Desde la maquina host podemos lanzar los diferentes elementos, en este caso un `eureka server` con un servicio que se balancea a 
traves de `zuul` y se tracea con `zipkin`. Cuando levantamos zuul en el `application.yaml` del directorio `resources` tenemos la
configuracion de donde esta el cliente eureka.

http://192.168.18.130:8761/
http://192.168.18.130:9001/hello?name=xavier
http://192.168.18.130:9000/zipkin/

```
vagrant@master:~/proyectos/spring-cloud-examples/spring-cloud-sleuth-zipkin/spring-cloud-zuul/src/main/resources$ more application.yml
server:
  port: 8888
spring:
  application:
    name: zuul
  zipkin:
    base-url: http://localhost:9000
  sleuth:
    sampler:
      percentage: 1.0
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```




