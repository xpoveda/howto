
Probando la arquitectura de Netflix
===================================

En este repositorio de github tenemos un monton de pequeños ejemplos con la arquitectura de spring cloud netflix.

[Ejemplos de Spring Cloud](https://github.com/ityouknow/spring-cloud-examples)

Nos podemos bajar este por ejemplo que muestra la interacción de `Eureka` con `Zuul`, `Sleuth` y `Zipkin`.
Para ello hemos de hacer un `git clone` del repositorio inicial y despues hacer desde spring tool suite  un `import maven project`
con la carpeta del proyecto concreto. En nuestro caso `C:\proyectos\spring-cloud-examples\spring-cloud-sleuth-zipkin`.

Despues haremos un `mvn install` de cada uno de los proyectos. Nos acordamos como siempre de modificar el buildpath y tambien de añadir
en el `pom.xml` de los 4 proyectos la siguiente dependencia justo debajo de `<dependencies>`porque sino da error en tiempo de ejecucion.

```
		<!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
		<dependency>
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
			<version>1.2</version>
		</dependency>
```

La explicación de este ejemplo concreto la podemos encontrar en `http://www.ityouknow.com/springcloud/2018/02/02/spring-cloud-sleuth-zipkin.html`.
Aparecerá en chino, la traducimos con Google Chrome.

Levantaremos los 4 proyectos con `run as sprinboot app` y despues podemos hacer `http://localhost:8761/` para acceder al servidor 
de `Eureka`. Con esta otra llamada `http://localhost:8888/producer/hello?name=xavier` lanzaremos la prueba que queremos monitorizar con
zipkin y para que ir al cliente de `Zipkin` lo haremos con `http://localhost:9000/zipkin/`.

En zipkin podemos ver las distintas llamadas realizadas com un todo entre los servicios. En nuestro caso el `zuul` se comunica con el 
`producer`.

