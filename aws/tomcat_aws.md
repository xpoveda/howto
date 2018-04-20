
Instalación en aws de tomcat 8
==============================

Instalación
-----------
Para la instalacion de tomcat8 seguiremos la guia de digital ocean que esta muy correcta pero que tiene algun pequeño error.

https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-8-on-ubuntu-16-04

Nos aseguramos que está el sistema actualizado y con java.
```
sudo apt-get update
sudo apt-get install default-jdk
```
Crearemos un usuario `tomcat` que sea el que utilice el servicio.
```
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
```
Instalaremos tomcat a partir de las paginas de descarga de tomcat porque la que se indica en el documento maestro no funciona.
Simplemente es ir a https://tomcat.apache.org/download-80.cgi y hacer boton derecho y "copiar direccion de enlace" en la version del `core` con extension `*.tar.gz`.

En nuestro caso nos bajaremos `http://mirror.olnevhost.net/pub/apache/tomcat/tomcat-8/v8.5.30/bin/apache-tomcat-8.5.30.tar.gz`
y lo copiaremos al directorio `tomcat`.
```
cd /tmp
curl -O http://mirror.olnevhost.net/pub/apache/tomcat/tomcat-8/v8.5.30/bin/apache-tomcat-8.5.30.tar.gz
sudo mkdir /opt/tomcat
sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
```
Damos permisos a este directorio para el usuario `tomcat`.
```
cd /opt/tomcat
sudo chgrp -R tomcat /opt/tomcat
sudo chmod -R g+r conf
sudo chmod g+x conf
sudo chown -R tomcat webapps/ work/ temp/ logs/
```
Modificaremos el servicio de tomcat para que sea un servicio de sistema y que por lo tanto lo podemos llamar automaticamente cuando reiniciemos
la instancia de EC2.
```
sudo update-java-alternatives -l
```
y vemos
```
Output
java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
```
Lo correcto es que el JAVAHOME de tomcat vaya contra el JRE.
```
JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
```
Asi que creamos el fichero `tomcat.service`
```
sudo nano /etc/systemd/system/tomcat.service
```
con la siguiente informacion
```
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```
Reiniciamos
```
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl status tomcat
```
Ya podremos acceder a la url de tomcat
```
curl http://localhost:8080
```
Y lo ponemos para que se inicie automaticamente.
```
sudo systemctl enable tomcat
```

Habilitar acceso desde cualquier IP tomcat administrador
--------------------------------------------------------
Para poder acceder a los distintos elementos del administrador de tomcat tendremos que modificar el `tomcat-users.xml` para crear usuarios y asignarles roles.
Ademas en cada una de las apps de gestion que llama internas modificar el fichero xml de configuracion para que se pueda acceder desde cualquier IP. Sino, por 
defecto el acceso a tomcat es solamente desde la maquina de localhost, cosa que por ejemplo desde maquinas ec2 linux de amazon es imposible.
```
root@ip-172-31-33-239:/opt/tomcat/conf# cat tomcat-users.xml
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
    version="1.0">

  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <user username="tomcat" password="tomcat" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>
</tomcat-users>
```
Modificaremos tambien estos otros dos ficheros.

```
/opt/tomcat/webapps/manager/META-INF/context.xml
/opt/tomcat/webapps/host-manager/META-INF/context.xml
```

Deshabilitando el `allow` para que no verifique que estamos llegando desde `127*`.
```
ubuntu@ip-172-31-33-239:~$ sudo /opt/tomcat/webapps/manager/META-INF/context.xml
sudo: /opt/tomcat/webapps/manager/META-INF/context.xml: command not found
ubuntu@ip-172-31-33-239:~$ sudo cat /opt/tomcat/webapps/manager/META-INF/context.xml
<?xml version="1.0" encoding="UTF-8"?>
<Context antiResourceLocking="false" privileged="true" >
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />

  -->
<Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

Para realizar la comprobacion del acceso podemos hacerlo baja una web que no haya cacheado nuestro resultado (cache de chrome o cache de proxy), desde el telefono mobil o incluso con lynx atacando
directamente a localhost (aunque ahi no veremos si el filtro por IP funciona o no).
