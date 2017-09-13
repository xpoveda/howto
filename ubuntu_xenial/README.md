
Configuración de ubuntu para trabajar con proxy corporativo
============================================================

Conectividad via sshó--------------------
Modificamos como root el fichero `/etc/profile`

```bash
export HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export FTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export http_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export https_proxy=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/
export ftp_proxy=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/
```

Y despues podemos probar la conectividad con

```bash
lynx http://google.es
curl http://google.es
```

#Asi ya funcionan la actualización de software con apt-get
/etc/apt/apt.conf

Acquire::http::proxy "http://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::https::proxy "https://USER:PASSWORD@PROXY_URL:PROXY_PORT/";
Acquire::ftp::proxy "ftp://USER:PASSWORD@PROXY_URL:PROXY_PORT/";

#Conectividad via graficos
Red\proxy de red\Manual
USER:PASSWORD@PROXY_URL PROXY_PORT

#Conectividad con Docker
#https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
#https://docs.docker.com/engine/admin/systemd/#httphttps-proxy

ubuntu@ubuntu:~$ more /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/" "NO_PROXY=localhost,127.0.0.1"

ubuntu@ubuntu:~$ sudo mkdir -p /etc/systemd/system/docker.service.d
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
ubuntu@ubuntu:~$ sudo vi /etc/systemd/system/docker.service.d/https-proxy.conf

ubuntu@ubuntu:~$ sudo systemctl daemon-reload
ubuntu@ubuntu:~$ sudo systemctl restart docker
ubuntu@ubuntu:~$ systemctl show --property=Environment docker
Environment=HTTP_PROXY=http://USER:PASSWORD@PROXY_URL:PROXY_PORT/ NO_PROXY=localhost,127.0.0.1 HTTPS_PROXY=https://USER:PASSWORD@PROXY_URL:PROXY_PORT/

#Conectividad con Maven

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

