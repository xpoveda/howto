Configurando Ubuntu para trabajar con proxy corporativo
=======================================================

A continuación anotamos los distintos elementos a configurar para la version de **Ubuntu 16.04.03**.

Conectividad via ssh
--------------------
Modificamos como root el fichero `/etc/profile`

```bash
export HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export FTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export http_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export https_proxy=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export ftp_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export no_proxy=localhost,127.0.0.0,127.0.1.1,127.0.1.1,local.home
```

Y despues podemos probar la conectividad con

```bash
lynx http://google.es
curl http://google.es
```

Configuración apt-get
---------------------
También tendremos que modificar el fichero `/etc/apt/apt.conf` para que los paquetes
se puedan bajar vía proxy.

```bash
Acquire::http::proxy "http://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::https::proxy "https://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::ftp::proxy "ftp://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
```

Conectividad escritorio
-----------------------
En el interfaz gráfico de ubuntu entraremos en `Red\proxy de red\Manual` y modificaremos tambien el `USER:PASSWORD@PROXY_URL PROXY_PORT`
que nos aparece en la ventana.

Conexión con Docker
-----------------------
Despues de instalar docker tendremos que configurar el proxy http y https para que podamos bajarnos
las imagenes de docker hub correctamente.

https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
https://docs.docker.com/engine/admin/systemd/#httphttps-proxy

Creamos los ficheros de configuración
```bash
ubuntu@ubuntu:~$ sudo mkdir -p /etc/systemd/system/docker.service.d
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/https-proxy.conf
```

Con los siguientes datos:
```bash
ubuntu@ubuntu:~$ more /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/" "NO_PROXY=localhost,127.0.0.1"
```

Y a continuación reiniciamos el daemon de docker.
```bash
ubuntu@ubuntu:~$ sudo systemctl daemon-reload
ubuntu@ubuntu:~$ sudo systemctl restart docker
ubuntu@ubuntu:~$ systemctl show --property=Environment docker
Environment=HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/ NO_PROXY=localhost,127.0.0.1 HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
```

Conectividad con Maven
----------------------

Maven tampoco usa directamente el proxy_http que se configura a nivel de sistema operativo sino que se ha de indicar
en el fichero `settings.xml` que se encuentra en la carpeta oculta de configuración de Maven `.m2`.

```bash
ubuntu@ubuntu:~/.m2$ more settings.xml
<settings>
  <proxies>
   <proxy>
      <id>proxy-descripcion-http</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>PROXY_URL</host>
      <port>PROXY_PORT</port>
      <username>USER</username>
      <password>PASSWORD</password>
      <nonProxyHosts></nonProxyHosts>
    </proxy>
   <proxy>
      <id>proxy-descripcion-https</id>
      <active>true</active>
      <protocol>https</protocol>
      <host>PROXY_URL</host>
      <port>PROXY_PORT</port>
      <username>USER</username>
      <password>PASSWORD</password>
      <nonProxyHosts></nonProxyHosts>
    </proxy>
  </proxies>
</settings>
```
