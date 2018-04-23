
Añadir subdominios a Apache securizados y que apunten a webapps de Tomcat
=========================================================================

Añadimos el `subdominio` a la `zona DNS` (route 53).
En este caso hemos añadido el subdominio `tomcat.xavierpoveda.com` que apuntará al administrador de tomcat.
Los registros `NS` y `SOA` son creados automaticamente por AWS.

Podemos ver estos datos con el uso de la herramienta `dig` de ubuntu.

```
root@ip-172-31-33-239:/etc/apache2/ssl/tomcat.xavierpoveda.com# dig xavierpoveda.com ANY

xavierpoveda.com.       60      IN      SOA     ns-268.awsdns-33.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
xavierpoveda.com.       60      IN      NS      ns-1000.awsdns-61.net.
xavierpoveda.com.       60      IN      NS      ns-1189.awsdns-20.org.
xavierpoveda.com.       60      IN      NS      ns-1904.awsdns-46.co.uk.
xavierpoveda.com.       60      IN      NS      ns-268.awsdns-33.com.
xavierpoveda.com.       60      IN      A       18.184.116.30
```
```
root@ip-172-31-33-239:/etc/apache2/ssl/tomcat.xavierpoveda.com# dig tomcat.xavierpoveda.com

tomcat.xavierpoveda.com. 56     IN      A       18.184.116.30
```

Creamos el fichero `tomcat.xavierpoveda.com.conf` en `/etc/apache2/sites-available` y definimos en los virtual host que `Apache` apunte a `Tomcat` 
mediante el `ajp13_worker` que tenemos definido en el `worker.properties`.

Mediante la libreria mod_jk realizamos el enlace de apache con tomcat mediante el uso de workers y el worker.properties.
Apache actua entonces como un proxy inverso que lo ke hace es capturar las peticiones y redirigirlas. 
De esta forma puede redirigir el contenido estatico hacia él y el dinamico hacia Tomcat mediante AJP13, resolviendose finalmente la 
peticion mediante el hostname y el docbase donde estamos apuntando.

Esto ultimo lo haremos con el uso adecuado de las rutas para capturar los recursos.
```
root@ip-172-31-33-239:/etc/apache2/sites-available# more tomcat.xavierpoveda.com.conf
<VirtualHost *:80>
  ServerName   tomcat.xavierpoveda.com
  ServerAlias  tomcat.xavierpoveda.com
  ServerAdmin  info@xavierpoveda.com

  JKMount /* ajp13_worker

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

<VirtualHost *:443>
  ServerName   tomcat.xavierpoveda.com
  ServerAlias  tomcat.xavierpoveda.com
  ServerAdmin  info@xavierpoveda.com

  JKMount /* ajp13_worker

  SSLEngine on
  SSLCertificateFile      /etc/apache2/ssl/tomcat.xavierpoveda.com/certificate.crt
  SSLCertificateKeyFile   /etc/apache2/ssl/tomcat.xavierpoveda.com/private.key
  SSLCertificateChainFile /etc/apache2/ssl/tomcat.xavierpoveda.com/ca_bundle.crt
</VirtualHost>
```

Generamos certificado en `http://sslforfree.com`. Los certificados de `let's encrypt` no soportan wildcards, para los subdominios
hemos de generarlos para cada uno de ellos. Marcamos verificacion por DNS manual creando un registro TXT en zona DNS de route 53.

Copiamos los certificados a `/etc/apache2/ssl/`
```
root@ip-172-31-33-239:/etc/apache2/ssl/tomcat.xavierpoveda.com# ls -lt | more
total 12
-rw-r--r-- 1 root root 1647 Apr 23 13:12 ca_bundle.crt
-rw-r--r-- 1 root root 1704 Apr 23 13:11 private.key
-rw-r--r-- 1 root root 2175 Apr 23 13:11 certificate.crt
```

Si es una aplicacion java, modificar `/opt/tomcat/conf/server.xml` apuntando en el elemento `<Host>` el subdominio al directorio donde tenemos la webapp.
```
root@ip-172-31-33-239:/etc/apache2/ssl/tomcat.xavierpoveda.com# more /opt/tomcat/conf/server.xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">

    <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
    <Connector port="8009" protocol="AJP/1.3"  redirectPort="8443" />

    <Engine name="Catalina" defaultHost="localhost">

      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <!--
      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
      -->

      <Host name="tomcat.xavierpoveda.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>


      <Host name="hello.xavierpoveda.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

         <Context path="" docBase="/opt/tomcat/webapps/examples" />
         <Alias>hello.xavierpoveda.com</Alias>

        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>


    </Engine>
  </Service>
</Server>
```

Habilitamos el sitio y reiniciamos Apache y Tomcat para que se cojan los cambios.
```
sudo -i

a2ensite tomcat.xavierpoveda.com

service apache2 reload
systemctl restart apache

service tomcat restart
```


Fuentes utilizadas
-------------------

```
https://www.digitalocean.com/community/questions/adding-a-subdomain-on-apache2
https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-8-on-ubuntu-16-04
https://www.digitalocean.com/community/tutorials/how-to-encrypt-tomcat-8-connections-with-apache-or-nginx-on-ubuntu-16-04
http://docs.lucee.org/guides/installing-lucee/lucee-server-adminstration-windows/utilizing-Tomcat-server-xml-file.html
```
