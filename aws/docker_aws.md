Docker en produccion aws
========================

Instalamos docker y maven con los mismos pasos que se indican en el documento madre.

Si falla alguno de los del docker no pasa nada, ir por el siguiente. La razon es que las versiones de ubuntu no son las mismas que las de vmware y hay librerias que no se necesitan y que Amazon ya cargó por defecto en la AMI.

Accedemos a este proyecto que nos descargaremos, generaremos ejecutable y crearemos la imagen docker en local.

https://github.com/dstar55/docker-hello-world-spring-boot
```
git clone https://github.com/dstar55/docker-hello-world-spring-boot
mvn clean install
mv ./target/hello*.jar ./data
docker build -t="hello-world-java" .
```

La ejecutamos
```
docker run -p 9000:8080 -it --rm hello-world-java
```
Previamente tendremos que levantar puertos para que lo podamos referenciar via subdominio y enlazar internamente desde apache como veremos mas abajo.

Tambien podriamos haber atacado directamente contra el puerto 9000.

Abrimos proxy de amazon para el puerto interno 9000 que es donde esta escuchando docker,  solo para la mascara ::/0 y nos aseguramos de que tenemos habilitado el modulo de redireccion de apache. Si lo queremos habilitar para el puerto externo es con la mascara 0.0.0.0/0

https://stackoverflow.com/questions/23931987/apache-proxy-no-protocol-handler-was-valid?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa

```
ubuntu@ip-172-31-33-239:/etc/apache2/sites-available$ cat test.xavierpoveda.com.conf
<VirtualHost *:80>

  ServerName   test.xavierpoveda.com
  ServerAlias  test.xavierpoveda.com
  ServerAdmin  info@xavierpoveda.com

    <Location />
       ProxyPass http://localhost:9000/
       ProxyPassReverse http://localhost:9000/
       Order allow,deny
       Allow from all
    </Location>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Y finalmente
```
ubuntu@ip-172-31-33-239:/etc/apache2/sites-available$ curl http://test.xavierpoveda.com
Hello World

ubuntu@ip-172-31-33-239:/etc/apache2/sites-available$ curl http://xavierpoveda.com:9000
^C

```


