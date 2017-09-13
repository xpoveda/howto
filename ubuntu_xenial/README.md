Configurando Ubuntu xenial desde 0
==================================

Aquí explicamos los elementos más importantes para configurar `Ubuntu xenial` como máquina virtual a partir de la `imagen ISO` que nos hemos descargado 
y lanzado con `Vmware workstation player 12`.

http://releases.ubuntu.com/16.04/ y elegir `64-bit PC (AMD64) desktop image`.

*Notas:*

 1. Para que funcione `Docker` la distro instalada ha de ser de 64 bits. 
    Algunos ordenadores no soportan el `modo VT-x` que es el necesario para poder correr la virtualizacion de 64 bits. 
    Hemos de asegurarnos que nuestro windows es de 64 bit (revisar el panel de control) y que tiene ese paquete activado, muchas veces esto último se configura directamente desde la BIOS.

 2. Si queremos instalar `Vagrant` en ubuntu para poder lanzar máquinas virtuales desde ubuntu con `Virtualbox` tambien tendremos que habilitar el modo VT-x
    que se encuentra en la configuración de la máquina virtual de Vmware en `Player\Manage\Virtual Machine Settings\Processors` (o similar) y hacer un click en el checkbox
    de `Virtualize Intel VT-x or AMD-V`.

Openssh
--------
Instalamos este paquete para podernos conectar via SSH con putty.

```bash
sudo apt-get install openssh-server
sudo service ssh status
```

El archivo de configuración lo tendremos aquí por si lo quisieramos modificar
```bash
sudo nano /etc/ssh/sshd_config
```

Y para reiniciar el servicio
```bash
sudo service ssh restart
```

Java 8
-------
Java 8 es la versión que utilizaremos para poder probar la gran mayoria de programas que implementemos y aplicaciones que vayamos a utilizar.

https://askubuntu.com/questions/762999/how-to-install-java-8-in-ubuntu-16-04

```bash
sudo apt-get install openjdk-8*

ubuntu@ubuntu:~$ java -version
openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2ubuntu1.16.04.3-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
```


Lynx
----
Lynx es un navegador en modo texto que nos servirá para comprobar la conectividad vía http/https.

```bash
sudo apt-get update
sudo apt-get install lynx
```

Docker
------
Los contenedores docker los instalaremos a partir de la última versión disponible de `docker ce` y de `docker-compose`.

### Docker-ce

https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
https://docs.docker.com/engine/admin/systemd/

```bash
sudo apt-get remove docker docker-engine docker.io
sudo apt-get update
sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
sudo docker run hello-world
```

### Docker compose
Más tarde instalaremos `docker compose` el cual nos permite crear más de una imagen docker a partir de un fichero `docker-compose.yml` YAML inicial
y de la definición propia de cada `Dockerfile`.


https://docs.docker.com/compose/install/
https://github.com/docker/compose/releases

```bash
sudo -i
curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

Maven
-----
Con maven podremos generar los ejecutables java a partir de la definición de un fichero `POM.xml`.

https://www.build-business-websites.co.uk/how-to-install-maven-3-on-ubuntu-16-04/

Instalamos la última versión de Maven
```bash
sudo apt-get update
sudo apt-get install maven
```

Comprobamos cual es
```bash
ubuntu@ubuntu:/opt$ mvn -version
Apache Maven 3.3.9
Maven home: /usr/share/maven
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.10.0-33-generic", arch: "amd64", family: "unix"
```

A veces al ejecutar Maven tendremos este error, que se solventará haciendo un *unset* de la variable `M2_HOME`.
```bash
ubuntu@ubuntu:~/mimaven$ mvn -version
Error: Could not find or load main class org.codehaus.plexus.classworlds.launcher.Launcher
```

Que se resuelve simplemente con :
```bash
https://stackoverflow.com/questions/11118237/maven-error-could-not-find-or-load-main-class-org-codehaus-plexus-classworlds-l
ubuntu@ubuntu:~/mimaven$ unset M2_HOME
```

Cliente FTP
-----------
Configuraremos el cliente FTP que viene por defecto con ubuntu, descomentando únicamente la instrucción `write_enable=YES` y reiniciando el servicio.

https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-a-user-s-directory-on-ubuntu-16-04

```
sudo nano /etc/vsftpd.conf
..
sudo systemctl restart vsftpd
```
