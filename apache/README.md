Apache tips
===========

Configuración php.ini por problemas en tamaño de subida en Wordpress
--------------------------------------------------------------------

Con esto no tendremos problemas en el upload de themes en wordpress sin problema.
Diferentes ficheros php.ini en la configuracion de plesk.

```bash
root@control:/etc/php5$ grep post_max_size ./apache2/php.ini
post_max_size = 101M

root@control:/etc/php5$ grep post_max_size ./cli/php.ini
post_max_size = 102M

root@control:/etc/php5$ grep post_max_size ./fpm/php.ini
post_max_size = 103M

root@control:/etc/php5$ grep post_max_size ./cgi/php.ini
post_max_size = 104M
```

Modificamos el post_max_size y el upload_max_filesize y despues lo comprobamos con info.php tal que

http://php.net/manual/es/function.phpinfo.php


