
Spring Cloud Netflix
====================

Introducción
------------
Recordar que la web de referencia para todo lo referente a `microservicios` es la web de [Martin Fowler](https://martinfowler.com/microservices/), aunque hay muchos escritos como este de [Kenny Bastani](http://www.kennybastani.com/2017/07/microservices-to-service-blocks-spring-cloud-function-aws-lambda.html).

En el canal de youtube de `Paradigma digital` hay mucha informacion sobre estos temas.

* [Introducción a Arquitecturas basadas en Microservicios](https://www.youtube.com/watch?v=2SnWpn1pCOs)
* [Arquitectura de microservicios de Spring-Cloud-NetFlix](https://www.youtube.com/watch?v=uWWKQhpGWPw)

EXCELENTE Referencia tambien es la web de [overclockyourself](https://overclockyourself.wordpress.com/2015/10/28/quien-es-quien-en-la-arquitectura-de-microservicios-de-spring-cloud-netflix/).

INICIAL en [Spring cloud en Autentia](https://www.adictosaltrabajo.com/tutoriales/introduccion-a-la-gestion-de-servicios-web-con-spring-cloud-y-netflix-oss/)

Y nuestro omnipresente ejemplo de [PiggyMetrics](https://github.com/sqshq/PiggyMetrics) donde podemos ver la arquitectura de spring cloud netflix dockerizada.

Muy buena documentación de [Pivotal devops workshop](https://github.com/Pivotal-Field-Engineering/devops-workshop)

Ejemplos de concepto
--------------------
<img src="https://www.adictosaltrabajo.com/wp-content/uploads/2017/03/ModeloReferencia.png"/>

En este repositorio de github tenemos un monton de pequeños ejemplos con la arquitectura de spring cloud netflix.

[Ejemplos de Spring Cloud](https://github.com/ityouknow/spring-cloud-examples)

Nos podemos bajar este por ejemplo que muestra la interacción de [Zuul](https://github.com/Netflix/zuul), [Eureka](https://github.com/Netflix/eureka), [Sleuth](https://cloud.spring.io/spring-cloud-sleuth/) y [Zipkin](https://github.com/openzipkin/zipkin).

Recordamos que la arquitectura de Netflix tambien esta compuesta por [Turbine](https://github.com/Netflix/Turbine), [Ribbon](https://github.com/Netflix/ribbon), [Spring cloud config](https://github.com/spring-cloud/spring-cloud-config) o su maravilloso [Hystrix](https://github.com/Netflix/Hystrix).

Para ello hemos de hacer un `git clone https://github.com/ityouknow/spring-cloud-examples` despues hacer desde spring tool suite  un `import maven project` con la carpeta del proyecto concreto que nos interese. 

En nuestro caso `C:\proyectos\spring-cloud-examples\spring-cloud-sleuth-zipkin`.

Despues haremos un `mvn install` de cada uno de los proyectos. Nos acordamos como siempre de modificar el `buildpath` y tambien de añadir en el `pom.xml` de los 4 proyectos la siguiente dependencia justo debajo de `<dependencies>` porque sino da error en tiempo de ejecucion.

```
<!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
<dependency>
	<groupId>commons-logging</groupId>
	<artifactId>commons-logging</artifactId>
	<version>1.2</version>
</dependency>
```

La explicación de este ejemplo concreto la podemos encontrar en `http://www.ityouknow.com/springcloud/2018/02/02/spring-cloud-sleuth-zipkin.html`. Aparecerá en chino, la traducimos con Google Chrome.

Levantaremos los 4 proyectos con `run as sprinboot app` y despues podemos hacer `http://localhost:8761/` para acceder al servidor 
de `Eureka`. Con esta otra llamada `http://localhost:8888/producer/hello?name=xavier` lanzaremos la prueba que queremos monitorizar con
zipkin y para que ir al cliente de `Zipkin` lo haremos con `http://localhost:9000/zipkin/`.

En zipkin podemos ver las distintas llamadas realizadas com un todo entre los servicios. En nuestro caso `Zuul` se comunica con el 
servicio `producer`.

