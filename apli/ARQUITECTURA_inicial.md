
ARQUITECTURA INICIAL
====================

Implementaré una o varias aplicaciones con el propósito de poner en práctica el conocimiento adquirido en los últimos meses sobre las distintas tecnologías que conforman las arquitecturas de computación en la nube (**CLOUD COMPUTING**).

## APLICACION

El lenguaje de programación utilizado será `JAVA 8`, que desarrolla los últimos paradigmas en **programación funcional**, **STREAMS** y **expresiones LAMBDA**. 

Se utilizarán también intensivamente todos los elementos que JAVA proporcione como el uso de **clases, clases abstractas, paquetes, interfaces, herencia, polimorfismo, GENERICS y manejo de excepciones**.

La construcción de los proyectos se realizará mediante `MAVEN`. 

Este lo sustituiremos en alguno de ellos por `GRADLE`, más moderno, el cual, mediante el uso de potentes scripts en `GROOVY`, nos dará una mayor flexibilidad en la construcción, testeo y empaquetado de nuestras aplicaciones. Estudiaremos  el **MANIFEST** de los distintos formatos comprimidos **JAR, WAR y EAR** que ofrece JAVA.

`ECLIPSE` o `SPRING TOOL SUITE` (según convenga), nos permitirán editar el código fuente de nuestra aplicación, que guardaremos remotamente en `GITHUB` y al que tendremos acceso directo al versionado de sus distintas ramas via interprete de comandos desde la consola, cmd windows, ssh linux o plugin de `GIT` que nos proporciona directamente el IDE.

El acceso a datos será, en distintas versiones de pruebas, con `JDBC` e `HIBERNATE`. Este último lo utilizaremos tanto en modo NATIVO como en modo implementación de `JPA`, mapeando en ambos casos el modelo de **BBDD relacional** como `MYSQL` (por ejemplo) con el modelo de objetos que es con el que trabaja JAVA. También realizaré pruebas con **BBDD NOSQL** como `MONGODB`.

Nuestra aplicación podría constar de una arquitectura cliente/servidor MVC en la que la capa servidor se implementa mediante **MICROSERVICIOS** en JAVA con `SPRING BOOT` y otros desarrollados en `PYTHON`, `KOTLIN` o `.NET` de esta forma desarrollaríamos una arquitectura basada en la interacción de **MICROSERVICIOS POLÍGLOTAS**.

Esta capa ofrecerá sus **SERVICIOS WEB** mediante `API RESTFUL` donde el intercambio de datos vía HTTP y en JSON proporcionará respuesta a aplicaciones cliente y hará posible también la mensajería entre los propios servicios que componen la aplicación servidora. El API REST lo podremos utilizar con PLUGINS de FIREFOX o CHROME como **HTTP REQUESTER** o incluso aplicaciones de testeo como **SOAPUI**.

Tambien implementaremos algún servicio web con tecnologia `JAX-WS SOAP`.

El servidor web será `APACHE TOMCAT`, el cual nos ofrecerá la posibilidad, además de hacer correr la aplicación, de mapearla en el **virtual host**  con el **nombre de dominio** con el que la queramos relacionar (DNS, fichero hosts).

<p align="center">
<img src="https://user-images.githubusercontent.com/13355927/31693920-8594c834-b3a1-11e7-9c2f-11a3a6d404ed.png"></img>
</p>

## INFRAESTRUCTURA

Cada vez que se realice la modificación de fuentes y se haga **COMMIT** en la rama principal se activarán **BUILDS automáticos**, es decir SCRIPTS `GROOVY` de `JENKINS` que nos proporciona un entorno de integración continua (**CONTINUOUS INTEGRATION**) y nos facilita a su vez la entrega continua (**CONTINUOUS DELIVERY**).

Para realizar las pruebas unitarias (**UNIT TESTING**), que es lo que nos posibilita la automatización de las pruebas y por lo tanto la validación automática de las entregas, utilizaremos las aserciones (**ASSERTS**) de `JUNIT`.

Nuestra aplicación se ejecutará tanto en el propio **LOCALHOST de WINDOWS**, en sistemas **LINUX UBUNTU virtualizados** o en el **mundo real colgando directamente de INTERNET**.

La aplicación se encapsulará en contenedores `DOCKER` que los tendremos almacenados en **repositorios de imágenes** como `DOCKER HUB` y que ejecutaremos en proveedores en nube como `AWS` dentro de una infraestructura mixta (pública y privada) que nos proporcione una red virtual `VPC` y máquinas virtuales `EC2` que podrían estar en CLUSTER mediante balanceadores `ELB`.

Una vez esté desarrollada la aplicación su puesta en producción nos tendría que dar igual fuera cual fuera de las distintas soluciones en la nube existentes hasta el momento: **AWS, GCE (GOOGLE CLOUD ENGINE), MICROSOFT AZURE, OPENSHIFT, HEROKU, DIGITAL OCEAN**….

La orquestación de contenedores la realizaremos preferiblemente con `KUBERNETES`. 

La capa cliente la implementaremos mediante `ANGULAR js` o cualquier otro FRAMEWORK que nos permita la interacción vía API con la capa servidor.

<p align="center">
<img src="https://user-images.githubusercontent.com/13355927/31693962-b64bc9dc-b3a1-11e7-8476-e587eb273fe2.png"></img>
</p>

ENLACES INTERESANTES
--------------------

https://github.com/xpoveda/howto/tree/master/cloud-computing

https://github.com/xpoveda/howto/tree/master/eclipse/docs

https://github.com/xpoveda/howto/blob/master/github/superguia.md

https://github.com/xpoveda/howto/tree/master/aws

https://github.com/xpoveda/howto/tree/master/gradle

https://github.com/xpoveda/howto/blob/master/maven/trabajando_con_maven.md

https://github.com/xpoveda/howto/tree/master/junit

https://github.com/xpoveda/howto/tree/master/jenkins

https://github.com/xpoveda/howto/tree/master/spring

https://github.com/eugenp/tutorials

https://spring.io/guides/gs/producing-web-service/

https://spring.io/guides/gs/consuming-web-service/

https://github.com/sqshq/PiggyMetrics

