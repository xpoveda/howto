Vagrant Multimachine plus
=========================

Instalando vagrant y virtualbox
-------------------------------
Antes de nada hay que instalar `vagrant` y `virtualbox` según se indica en https://github.com/xpoveda/howto/blob/master/vagrant/vagrant.md.

Añadiendo el plugin para poder ir a traves de proxy
---------------------------------------------------
Mediante la instalación de este plugin http://tmatilai.github.io/vagrant-proxyconf/ es posible trabajar a través de proxy corporativo, sin tener que tocar nada en la configuración de las maquinas virtuales, unicamente modificando el `Vagrantfile`. Con esto tendremos modificación a nivel de proxy de lo habitual para poder bajarnos paquetes con `apt-get` o navegar con `lynx`.

Si existe cualquier otro problema que nos indique que es un problema de proxy podemos visitar https://github.com/xpoveda/howto/tree/master/proxy.

Para deshabilitar el proxy haremos un `vagrant reload` despues de haber comentado la parte del proxy.


Cómo funciona Vagrant
----------------------
Vagrant nos ayuda a construir un entorno virtualizado a partir de la definición del fichero de configuración `Vagrantfile`.
Para ello hará uso de imagenes de maquinas virtuales (a los que llamaremos boxes) que estan en la propia web de Hashicorp y de las sentencias del fichero de configuracion. Con todo esto podremos crear redes privadas y publicas virtuales, y a partir de aquí clusteres para constituir un primer entorno de desarrollo para nuestra aplicación.

Los boxes se bajarán automáticamente la primera vez que intentemos ejecutar el fichero de configuración.
Sino tambien los podemos bajar directamente con, por ejemplo, `vagrant box add bento/ubuntu-16.04`. Con `vagrant box list` veremos los boxes que tenemos instalados.

Virtualbox
----------
Vagrant se basa en la ejecución de maquinas virtuales en múltiples sistemas, uno de ellos es `Virtualbox`, que será con el que trabajaremos aquí. Se hace notar que el refresco en Virtualbox no es demasiado bueno en la versión de windows por lo que la forma de poder trabajar con las maquinas virtuales creadas desde Virtualbox será que estén paradas primero de todo en vagrant con el comando `vagrant halt nombre_maquina` y comprobandolo posteriormente con `vagrant status`. Si quisieramos destruir una maquina utilizariamos `vagrant destroy VM`.

A continuación haremos un `Machine + add` en Virtuabox localizando el fichero `*.box` donde está situado la máquina. Habitualmente estará en `C:\Users\XXXXXXXX\VirtualBox VMs`.


Instalando una maquina
-----------------------
Vamos a `c:\Hashicorp\Vagrant\bin` y ahi haremos un `vagrant init` lo cual nos creará un `Vagrantfile` por defecto. Ese fichero lo modificaremos por el que queramos y a continuación un `vagrant up` que nos creará el entorno.

Vagrantfile
-----------
Este fichero crea una configuración de 2 maquinas virtuales dentro de una red privada.

Además de que son accesibles entre si pueden ser atacadas desde windows desde un determinado puerto (en este caso el 8080 que redirigirá hacia el puerto 80 en el que tendremos instalado un apache.

En este ejemplo la red que se configurará sera una 10.10.1.0/24

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_IMAGE = "bento/ubuntu-16.04"

Vagrant.configure("2") do |config|

  config.vm.define "ma1" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "ma1"
      subconfig.vm.network :private_network, ip: "10.10.1.1"

      #host es windows, guest es linux
      subconfig.vm.network :forwarded_port, host: 8080, guest: 80, auto_correct: true
  end

  config.vm.define "ma2" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "ma2"
      subconfig.vm.network :private_network, ip: "10.10.1.2"
      subconfig.vm.network :forwarded_port, host: 8081, guest: 80, auto_correct: true
  end  


  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://PROXY:PORT
    config.proxy.https    = "http://PROXY:PORT"
    config.proxy.no_proxy = "localhost,127.0.0.1,10.10.1.1,10.10.1.2"
  end
  	
  # Install avahi on all machines 
  config.vm.provision "shell", inline: <<-SHELL 
  apt-get install -y avahi-daemon libnss-mdns
  SHELL
end
```
Añadiendo la regla en el proxy de windows
-----------------------------------------
En el proxy de windows hay que indicar que NO coja los elementos 10.10.1.1 ni 10.10.1.2, ya que forman parte de nuestro sistema local.

Instalando apache y lynx en linux
---------------------------------
```
sudo -i
apt-get install apache2
apt-get install lynx
```

El log para comprobar las conexiones de apache está en `/var/log/apache2/access.log`.

Comprobando la conectividad con nc
----------------------------------
La herramienta `nc` de linux nos permite comprobar la conectividad entre maquinas de una forma muy poderosa https://linux.die.net/man/1/nc.

Nos ponemos a escuchar en el puerto 1234 en la maquina `ma2`
```
#root@ma2:~# nc -l 1234

```

En la maquina `ma1` hemos escrito un fichero y se lo enviamos a la maquina `ma2`.
```
root@ma1:~# nc 10.10.1.2 1234 < hola.txt
```

El resultado es que, como hay conectividad, llegará el contenido del fichero a la maquina `ma2`
```
root@ma2:~# nc -l 1234

hola
```

Más comandos para comprobar la conectividad
```
ping 10.10.1.1

dig sudandomarketing.com

telnet google.com 80

telnet 10.10.1.1 22
Trying 10.10.1.1...
Connected to 10.10.1.1.
Escape character is '^]'.
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.4
```

Accediendo via http al servidor
-------------------------------
Accedemos desde el navegador de windows con `http://10.10.1.1:8080` o desde ssh de cada una de las maquinas con `lynx http://10.10.1.1` o `curl http://10.10.1.1`.
