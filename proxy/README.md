Configurando Ubuntu para trabajar con proxy corporativo
=======================================================

A continuación anotamos los distintos elementos a configurar para la version de **Ubuntu 16.04.03**.

Conectividad via ssh
--------------------
Modificamos como root el fichero `/etc/profile`

```bash
export HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export FTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export http_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export https_proxy=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export ftp_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export no_proxy=localhost,127.0.0.0,127.0.1.1,127.0.1.1,local.home
```

Y despues podemos probar la conectividad con

```bash
lynx http://google.es
curl http://google.es
```

Configuración apt-get
---------------------
También tendremos que modificar el fichero `/etc/apt/apt.conf` para que los paquetes
se puedan bajar vía proxy.

```bash
Acquire::http::proxy "http://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::https::proxy "https://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::ftp::proxy "ftp://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
```

Conectividad escritorio
-----------------------
En el interfaz gráfico de ubuntu entraremos en `Red\proxy de red\Manual` y modificaremos tambien el `USER:PASSWORD@PROXY_URL PROXY_PORT`
que nos aparece en la ventana.

Conexión con Docker
-----------------------
Despues de instalar docker tendremos que configurar el proxy http y https para que podamos bajarnos
las imagenes de docker hub correctamente.

https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
https://docs.docker.com/engine/admin/systemd/#httphttps-proxy

Creamos los ficheros de configuración
```bash
ubuntu@ubuntu:~$ sudo mkdir -p /etc/systemd/system/docker.service.d
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/https-proxy.conf
```

Con los siguientes datos:
```bash
ubuntu@ubuntu:~$ more /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/" "NO_PROXY=localhost,127.0.0.1"
```

Y a continuación reiniciamos el daemon de docker.
```bash
ubuntu@ubuntu:~$ sudo systemctl daemon-reload
ubuntu@ubuntu:~$ sudo systemctl restart docker
ubuntu@ubuntu:~$ systemctl show --property=Environment docker
Environment=HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/ NO_PROXY=localhost,127.0.0.1 HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
```

Conectividad con Maven
----------------------

Maven tampoco usa directamente el proxy_http que se configura a nivel de sistema operativo sino que se ha de indicar
en el fichero `settings.xml` que se encuentra en la carpeta oculta de configuración de Maven `.m2`.

```bash
ubuntu@ubuntu:~/.m2$ more settings.xml
<settings>
  <proxies>
   <proxy>
      <id>proxy-descripcion-http</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>PROXY_URL</host>
      <port>PROXY_PORT</port>
      <username>USER</username>
      <password>PASSWORD</password>
      <nonProxyHosts></nonProxyHosts>
    </proxy>
   <proxy>
      <id>proxy-descripcion-https</id>
      <active>true</active>
      <protocol>https</protocol>
      <host>PROXY_URL</host>
      <port>PROXY_PORT</port>
      <username>USER</username>
      <password>PASSWORD</password>
      <nonProxyHosts></nonProxyHosts>
    </proxy>
  </proxies>
</settings>
```

Conectividad con Gradle
------------------------
```
$ curl -s "https://get.sdkman.io" | bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
$ sdk version

ubuntu@ubuntu:~$ sdk install gradle


ubuntu@ubuntu:~$ gradle -version

------------------------------------------------------------
Gradle 4.1
------------------------------------------------------------

Build time:   2017-08-07 14:38:48 UTC
Revision:     941559e020f6c357ebb08d5c67acdb858a3defc2

Groovy:       2.4.11
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_131 (Oracle Corporation 25.131-b11)
OS:           Linux 4.10.0-33-generic amd64

ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ more gradle.properties
systemProp.http.proxyHost=PROXY
systemProp.http.proxyPort=PROXY_PORT
systemProp.http.proxyUser=USER
systemProp.http.proxyPassword=PASSWORD
systemProp.https.proxyHost=PROXY
systemProp.https.proxyPort=PROXY_PORT
systemProp.https.proxyUser=USER
systemProp.https.proxyPassword=PASSWORD
```

```
https://spring.io/guides/gs/spring-boot/
```

```
ubuntu@ubuntu:~/misproyectos/gs-spring-boot/complete$ gradle clean build

> Task :test
2017-09-18 09:17:47.644  INFO 2457 --- [       Thread-6] ationConfigEmbeddedWebApplicationContext : Closing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@678b56a5: startup date [Mon Sep 18 09:17:36 PDT 2017]; root of context hierarchy
2017-09-18 09:17:47.655  INFO 2457 --- [       Thread-9] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@7094bfce: startup date [Mon Sep 18 09:17:44 PDT 2017]; root of context hierarchy


BUILD SUCCESSFUL in 16s
7 actionable tasks: 7 executed
```
