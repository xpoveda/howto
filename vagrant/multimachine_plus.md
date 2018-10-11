

Instalando vagrant y virtualbox
-------------------------------
Antes de nada hay que instalar `vagrant` y `virtualbox` según se indica en https://github.com/xpoveda/howto/blob/master/vagrant/vagrant.md.

Añadiendo el plugin para poder ir a traves de proxy
---------------------------------------------------
Mediante la instalación de este plugin http://tmatilai.github.io/vagrant-proxyconf/ es posible trabajar a través de proxy corporativo, sin tener que tocar nada en la configuración de las maquinas virtuales, unicamente modificando el `Vagrantfile`.

Cómo funciona Vagrant
----------------------
Vagrant nos ayuda a construir un entorno virtualizado a partir de la definición del fichero de configuración `Vagrantfile`.
Para ello hará uso de imagenes de maquinas virtuales (a los que llamaremos boxes) que estan en la propia web de Hashicorp y de las sentencias del fichero de configuracion. Con todo esto podremos crear redes privadas y publicas virtuales, y a partir de aquí clusteres para constituir un primer entorno de desarrollo para nuestra aplicación.

Los boxes se bajarána automáticamente la primera vez que intentemos ejecutar el fichero de configuración.
Sino tambien los podemos bajar directamente con, por ejemplo, `vagrant box add bento/ubuntu-16.04`. Con `vagrant box list` veremos los boxes que tenemos instalados.

Virtualbox
----------
Vagrant se basa en la ejecución de maquinas virtuales en múltiples sistemas, uno de ellos es `Virtualbox, que será con el que trabajaremos
aquí. Se hace notar que el refresco en Virtualbox no es demasiado bueno en la versión de windows por lo que la forma de poder trabajar
con las maquinas virtuales creadas desde Virtualbox será que estén paradas primero de todo en vagrant con el comando `vagrant halt nombre_maquina` y comprobandolo posteriormente con `vagrant status`. Si quisieramos destruir una maquina utilizariamos `vagrant destroy VM`.









