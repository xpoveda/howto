
Instalar web segura en EC2 de AWS
=================================

Habilitar puertos
-----------------
En el "security groups" añadimos las reglas que permitan puerto 80 y puerto 443 en el incoming. 
Estos puertos se añadiran al 22 ya existente.

En el outgoing ha de estar como "all".

No hace falta actualizar el firewall del propio ubuntu porque estos "security groups" es un firewall a nivel de cloud.

Crear zona DNS en Amazon
------------------------
Entra en el servicio de route 53 y crea una "Hosted zone".

Con eso tendremos una zona de DNS que será sobre la que tendremos que apuntar a los name servers que nos indica desde el hosting 
donde tenemos registrado el dominio.

Añade en route 53 nuevo "Record set" a esa "Hosted zone".
Con añadir un registro de tipo "A" que apunte a la IP donde tendremos la web ya es suficiente.
Esa IP es la "IPv4 Public IP" que se indica en los datos de la EC2 de Amazon.

Creacion de certificados
------------------------
Crearemos los certificados con la web https://www.sslforfree.com entrando un nombre de dominio con la que podremos tener unos certificados de let's encrypt gratuitos para
poder probar 3 meses y la instalacion seria identica a la de certificados comprados, ya que nos genera 3 ficheros que tendremos que cargar en el servidor
y referenciar desde el virtual host de configuracion del dominio que toque.

La forma para autentificarnos es via registros DNS. Crea los registros DNS de tipo TXT que te indica, verifica y despues pide que te los genere.

Instala Apache con ssl activado
-------------------------------

```
sudo apt-get update
sudo apt-get install apache2
```

y habilitamos el ssl
```

sudo a2enmod ssl
```

Creamos los virtualhosts
------------------------
```
sudo mkdir -p /var/www/xavierpoveda.com/public_html
cd /var/www/html
sudo cp index.html /var/www/xavierpoveda.com/public_html/
cd /var/www/xavierpoveda.com/public_html/
sudo vi index.html
sudo chmod -R 755 /var/www
cd /var/www

ls -lt | more
total 8
drwxr-xr-x 3 root root 4096 Apr 19 13:54 xavierpoveda.com
drwxr-xr-x 2 root root 4096 Apr 19 12:46 html
```

Modificamos el fichero de configuracion para que tanto capture las peticiones por el puerto 80 como por el 443.
```
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/xavierpoveda.com.conf
```

Modificamos el fichero de configuracion del nuevo dominio, habiendo subido previamente los ficheros de certificados
```
ubuntu@ip-172-31-33-239:~$ more /etc/apache2/sites-available/xavierpoveda.com.conf
<VirtualHost *:80>
	ServerName xavierpoveda.com
	ServerAlias www.xavierpoveda.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/xavierpoveda.com/public_html

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:443>
	ServerName xavierpoveda.com
	ServerAlias www.xavierpoveda.com
	DocumentRoot /var/www/xavierpoveda.com/public_html
	SSLEngine on
	SSLCertificateFile /etc/apache2/ssl/xavierpoveda.com/certificate.crt
	SSLCertificateKeyFile /etc/apache2/ssl/xavierpoveda.com/private.key
	SSLCertificateChainFile /etc/apache2/ssl/xavierpoveda.com/ca_bundle.crt
</VirtualHost>
```

Lo habilitamos, hacemos un disable del default y hacemos un reload de apache.
```
sudo a2ensite xavierpoveda.com.conf
sudo a2dissite 000-default.conf
sudo service apache2 reload
```

URLS interesantes
-----------------
https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-16-04
https://comodosslstore.com/ssltools/

