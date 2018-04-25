SPRINGBOOT AWS
==============

Instalación de mysql
--------------------
Instalamos `mysql` en nuestro VPS de cualquier hosting o EC2 de AWS y securizamos `phpmyadmin` según los siguientes enlaces

https://www.digitalocean.com/community/tutorials/how-to-install-the-latest-mysql-on-ubuntu-16-04

https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-16-04

De esta forma ya podremos acceder a phpmyadmin entrando el usuario y contraseña que hemos marcado en la instalación.
```
http://xavierpoveda.com/phpmyadmin/ 
```

Creamos usuario y bbdd asociada "test-oapc", que es a la que está apuntando nuestro proyecto.

Descarga y generación del proyecto
----------------------------------
Hacemos un `git clone`y un `mvn clean` y `mvn install` para generarlo.
```
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ mvn clean
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ mvn install
```

Ejecución del servidor
-----------------------
El resultado es un `jar` que será el ejecutable de springboot, el cual tiene un tomcat interno.

```
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ java -jar ./target/spring-boot-oapc-0.1.0-SNAPSHOT.jar
```

Modificación configuración para escucha por puerto específico
-------------------------------------------------------------
Modificaremos el `application.properties` para que el servidor escuche por el puerto `8090`, en lugar del por el `8080` que es el puerto por defecto.

```
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server/src/main/resources$ more application.properties
#----------------------------------------------------------------------------
app.name = springboot-oapc-server
#----------------------------------------------------------------------------
jwt.header            = Authorization
jwt.expires_in        = 600
jwt.mobile_expires_in = 600
jwt.secret            = S3cr3t0
#----------------------------------------------------------------------------
spring.datasource.url         = jdbc:mysql://localhost:3306/test-oapc
spring.datasource.username    = test-oapc
spring.datasource.password    = test-oapc
#----------------------------------------------------------------------------
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect

spring.datasource.initialize  = true
# possible values: validate | update | create | create-drop
#Primera vez con "create" para que cree tablas y ejecute el import.sql
#spring.jpa.hibernate.ddl-auto = create
#Resto de veces con "update" para que actualice datos
spring.jpa.hibernate.ddl-auto = update

server.port = 8090
server.use-forward-headers = true
```

Y habilitaremos en apache el modulo de `headers` para que podamos redireccionar las llamadas al puerto `443` contra el puerto `8090` que esta a la escucha, rescatando las cabeceras de seguridad.

Este cambio tambien implica modificar el fichero `aplication.properties` del springboot con la sentencia `server.use-forward-headers = true` asi como el `api.xavierpoveda.com.conf` en la sección del virtualhost que sirve el 443.

```
sudo a2enmod headers
sudo service apache restart
```

El fichero de configuracion queda como se muestra a continuacion.

Recordar que se ha de generar con `sslforfree.com` un certificado para el subdominio `api`.

En este ejemplo tenemos tambien que las llamadas al puerto `80` redireccionan el mismo puerto `8090`.
```
ubuntu@ip-172-31-33-239:/etc/apache2/sites-available$ more api.xavierpoveda.com.conf
<VirtualHost *:80>
    ServerName   api.xavierpoveda.com
    ServerAlias  api.xavierpoveda.com
    ServerAdmin  info@xavierpoveda.com

    <Location />
        ProxyPass        http://localhost:8090/
        ProxyPassReverse http://localhost:8090/
        Order allow,deny
        Allow from all
    </Location>


    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

<VirtualHost *:443>
    ServerName  api.xavierpoveda.com
    ServerAlias api.xavierpoveda.com

    <Location />
        RequestHeader set X-Forwarded-Proto https
        RequestHeader set X-Forwarded-Port 443
        ProxyPreserveHost On

        ProxyPass        http://localhost:8090/
        ProxyPassReverse http://localhost:8090/
        Order allow,deny
        Allow from all
    </Location>

    SSLEngine on
    SSLCertificateFile      /etc/apache2/ssl/api.xavierpoveda.com/certificate.crt
    SSLCertificateKeyFile   /etc/apache2/ssl/api.xavierpoveda.com/private.key
    SSLCertificateChainFile /etc/apache2/ssl/api.xavierpoveda.com/ca_bundle.crt
</VirtualHost>
```

Si queremos modificar el funcionamiento del puerto 80 podemos hacer que redireccione siempre al 443.

Y siempre para reiniciar apache
```
sudo service apache2 reload
```



Con este añadido de subdominio lo que hacemos es que las peticiones que se harian mediante: `http://xavierpoveda.com:8090/api/login` ahora se hacen con `http://api.xavierpoveda.com/api/login`.

Y con la escucha del puerto 8443 lo que hacemos es poder habilitar el https del API `https://api.xavierpoveda.com/api/login`

Esta seria una `request` si vamos contra el endpoint `login`.
```
Content-Type:application/json
Body raw application/json
{"username":"admin","password":"123"}
```

y esta una `response`con el token.
```
{
    "access_token": "eyJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzcHJpbmdib290LW9hcGMtc2VydmVyIiwic3ViIjoiYWRtaW4iLCJhdWQiOiJ3ZWIiLCJpYXQiOjE1MjQ2NDk2NzYsImV4cCI6MTUyNDY1MDI3Nn0.Nu2ESXxrOJmIORJORkGQpc33VJI0j1JN5Q0FFOZkyXy1mE4nlpZPcOkjdTRUzSJZg4qv9RXdsSQExMGhNqW0Aw",
    "expires_in": 600
}
```

Y este contra `whoami` mediante la URL `https://api.xavierpoveda.com/api/whoami`
```
Content-Type:application/json
Authorization:Bearer eyJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzcHJpbmdib290LW9hcGMtc2VydmVyIiwic3ViIjoiYWRtaW4iLCJhdWQiOiJ3ZWIiLCJpYXQiOjE1MjQ2NDkzMTYsImV4cCI6MTUyNDY0OTkxNn0.ujZy-wdhcn8_4VVPhjTagpcwnYVWxOYG2Ek85cOIuYUQcTp_WEKa3qNJ51Xij55aM3OHVtOyucNLAWNSvIAbJA
```
