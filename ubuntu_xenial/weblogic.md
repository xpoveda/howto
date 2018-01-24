
Weblogic en ubuntu xenial
=========================


Instalación de java8 de oracle
------------------------------

Antes de nada hemos de instalar el oracle de java.

https://www.digitalocean.com/community/tutorials/como-instalar-java-con-apt-get-en-ubuntu-16-04-es

```
sudo apt-get update
sudo apt-get install default-jre
sudo apt-get install default-jdk
```

```
export http_proxy=LOQUETOCA
export HTTP_PROXY=LOQUETOCA
export https_proxy=LOQUETOCA
export HTTPS_PROXY=LOQUETOCA
```

```
sudo -E add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

```
ubuntu@ubuntu:~$ sudo update-alternatives --config java
There are 2 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                            Priority   Status

  0            /usr/lib/jvm/java-8-oracle/jre/bin/java          1081      auto mode
  1            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode
* 2            /usr/lib/jvm/java-8-oracle/jre/bin/java          1081      manual mode
```

```
sudo nano /etc/environment
JAVA_HOME="/usr/lib/jvm/java-8-oracle"
source /etc/environment
echo $JAVA_HOME
```


Instalación de weblogic
-----------------------

http://www.oracle.com/technetwork/middleware/weblogic/downloads/wls-for-dev-1703574.html

Nos bajamos la version generica y la subimos por FTP a UNIX.
Hemos de lanzarlo desde un entorno visual de UNIX, en nuestro caso trabajamos con Ubuntu xenial lanzado desde VMWARE.
Se puede hacer igual desde un virtualbox.

```
Oracle WebLogic Server 12.1.3
 Installers with Oracle WebLogic Server and Oracle Coherence:
DownloadGeneric (881 MB) 
```

Como usuario no administrador la instalamos

```
ubuntu@ubuntu:~/Downloads$ java -jar fmw_12.1.3.0.0_wls.jar
```

Y le damos a todo "Next". Hemos de elegir la opcion de "weblogic server" cuando nos lo pida.
En el ultimo paso decimos que configuramos los usuarios y passwords.


```
ubuntu@ubuntu:~$ more ./lanza_weblogic.sh
/home/ubuntu/Oracle/Middleware/Oracle_Home/user_projects/domains/base_domain/startWebLogic.sh
ubuntu@ubuntu:~$ sudo ./lanza_weblogic.sh
```

Desde windows en un explorador
```
http://192.168.140.133:7001/console
http://192.168.140.133:7001/console/login/LoginForm.jsp
```
