
Hacemos un `git clone https://github.com/sqshq/PiggyMetrics.git` del repositorio de PiggyMetrics y con esta instrucción podemos regenerar los paquetes en local con maven mas tarde.

Abundante información en: *https://github.com/sqshq/PiggyMetrics*

```
ubuntu@ubuntu:~/mijava/PiggyMetrics$ mvn package -DskipTests
[INFO] ------------------------------------------------------------------------
[INFO] Building piggymetrics 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] config ............................................. SUCCESS [01:14 min]
[INFO] monitoring ......................................... SUCCESS [ 55.906 s]
[INFO] registry ........................................... SUCCESS [ 14.388 s]
[INFO] gateway ............................................ SUCCESS [ 10.887 s]
[INFO] auth-service ....................................... SUCCESS [ 32.568 s]
[INFO] account-service .................................... SUCCESS [ 19.138 s]
[INFO] statistics-service ................................. SUCCESS [ 18.292 s]
[INFO] notification-service ............................... SUCCESS [  9.638 s]
[INFO] piggymetrics ....................................... SUCCESS [  0.003 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 03:58 min
[INFO] Finished at: 2017-09-05T09:11:43-07:00
[INFO] Final Memory: 79M/189M
[INFO] ------------------------------------------------------------------------
```

Vemos los jar generados.
```
ubuntu@ubuntu:~/mijava/PiggyMetrics$ find . -name *.jar | grep target
./notification-service/target/notification-service-1.0.0-SNAPSHOT.jar
./notification-service/target/notification-service.jar
./config/target/config-1.0.0-SNAPSHOT.jar
./config/target/config.jar
./auth-service/target/auth-service-1.0-SNAPSHOT.jar
./auth-service/target/auth-service.jar
./statistics-service/target/statistics-service.jar
./statistics-service/target/statistics-service-1.0-SNAPSHOT.jar
./monitoring/target/monitoring.jar
./monitoring/target/monitoring-0.0.1-SNAPSHOT.jar
./account-service/target/account-service-1.0-SNAPSHOT.jar
./account-service/target/account-service.jar
./registry/target/registry.jar
./registry/target/registry-0.0.1-SNAPSHOT.jar
./gateway/target/gateway.jar
./gateway/target/gateway-1.0-SNAPSHOT.jar
```


Si regeneramos las imagenes docker con `docker-compose en local` podemos ver lo que deja.
Que seria lo mismo a si lo ejecutamos con las imagenes que nos hemos bajado de `docker hub` del usuario `sqshq`.
Generar en local sirve para poder modificar el código, sino esta claro que solo podremos utilizar la funcionalidad de la imagen
generada por el autor.

Exportamos las variables de entorno antes de lanzar nada
```
export CONFIG_SERVICE_PASSWORD="hola" && export NOTIFICATION_SERVICE_PASSWORD="hola" &&  export STATISTICS_SERVICE_PASSWORD="hola" && export ACCOUNT_SERVICE_PASSWORD="hola" && export MONGODB_PASSWORD="hola"

root@ubuntu:/home/ubuntu/mijava/PiggyMetrics# env | grep PASS
STATISTICS_SERVICE_PASSWORD=hola
MONGODB_PASSWORD=hola
ACCOUNT_SERVICE_PASSWORD=h
NOTIFICATION_SERVICE_PASSWORD=hola
CONFIG_SERVICE_PASSWORD=hola
```

Script de lanzamiento de la versión que directamente baja todas las imagenes del registro de Docker hub y ejecuta la aplicación.
Simplemente nos tendremos que bajar el fichero docker-compose.yml de Github y ejecutar:
```
docker-compose up
```

O script para lanzar la version de desarrollo, que creará la imagenes a partir de los ficheros del directorio PiggyMetrics.
```
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up
```

**Importante borrar los contenedores y las imagenes si queremos ir de una versión a otra para que no quede el sistema inconsistente.**


```
root@ubuntu:/home/ubuntu/mijava/PiggyMetrics# docker images
REPOSITORY                                TAG                 IMAGE ID            CREATED             SIZE
jenkins/jenkins                           lts                 aca6340878dd        5 days ago          840MB
tomcat                                    8.0                 cff8f1afd8d3        10 days ago         357MB
rabbitmq                                  3-management        fb11f4e0a6b6        2 weeks ago         124MB
ubuntu                                    latest              ccc7a11d65b1        3 weeks ago         120MB
tomcat                                    latest              72d2be374029        3 weeks ago         292MB
sqshq/piggymetrics-mongodb                latest              b55db054a057        4 weeks ago         359MB
sqshq/piggymetrics-monitoring             latest              a7761d8d76a1        4 weeks ago         352MB
sqshq/piggymetrics-notification-service   latest              70a2175edb65        4 weeks ago         355MB
sqshq/piggymetrics-statistics-service     latest              ec50b5ccb707        4 weeks ago         354MB
sqshq/piggymetrics-account-service        latest              68cab8066898        4 weeks ago         354MB
sqshq/piggymetrics-auth-service           latest              645331191665        4 weeks ago         350MB
sqshq/piggymetrics-gateway                latest              02db2d98e396        4 weeks ago         349MB
sqshq/piggymetrics-registry               latest              400923ce16dc        4 weeks ago         348MB
sqshq/piggymetrics-config                 latest              977360c8f3bd        4 weeks ago         333MB
hello-world                               latest              1815c82652c0        2 months ago        1.84kB
springcloud/eureka                        latest              3094afcbdf12        2 years ago         660MB
```

Sustituimos localhost por la IP que nos marca ifconfig.
```
http://localhost:80 - Gateway
http://localhost:8761 - Eureka Dashboard
http://localhost:9000/hystrix - Hystrix Dashboard (paste Turbine stream link on the form)
http://localhost:8989 - Turbine stream (source for the Hystrix Dashboard)
http://localhost:15672 - RabbitMq management (default login/password: guest/guest)
```

Donde la URL que le pasaremos a hystrix es `http://localhost:8989/hystrix.stream`
