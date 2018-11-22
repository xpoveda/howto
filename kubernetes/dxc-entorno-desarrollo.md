Entorno de desarrollo en local
==============================

Prometheus
==========

https://prometheus.io/docs/prometheus/latest/installation/

```
docker pull prom/prometheus
```

Cadvisor
========

https://prometheus.io/docs/guides/cadvisor/

```
root@master:~/proyectos/cadvisor# cat prometheus.yml
scrape_configs:
- job_name: cadvisor
  scrape_interval: 5s
  static_configs:
  - targets:
    - cadvisor:9091
```

```
root@master:~/proyectos/cadvisor# cat docker-compose.yml
version: '3.2'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
    - 9090:9090
    command:
    - --config.file=/etc/prometheus/prometheus.yml
    volumes:
    - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
    depends_on:
    - cadvisor
  cadvisor:
    image: google/cadvisor:latest
    container_name: cadvisor
    ports:
    - 9091:8080
    volumes:
    - /:/rootfs:ro
    - /var/run:/var/run:rw
    - /sys:/sys:ro
    - /var/lib/docker/:/var/lib/docker:ro
    depends_on:
    - redis
  redis:
    image: redis:latest
    container_name: redis
    ports:
    - 6379:6379
```

```
root@master:~/proyectos/cadvisor# docker-compose up -d
```


Grafana
==========
```
root@master:~# docker run -d -p 3000:3000 grafana/grafana
```

1) Accedemos a grafana por la IP y el puerto 3000. El usuario sera admin/admin y despues lo modificaremos.

2) Vamos a Confiuration + Data Sources y hacemos un añadir Prometheus con nombre Prometheus accediendo por la IP donde tenemos instalado Prometheus y el puerto 9090

3) Extraer el json para hacer dashboard https://grafana.com/dashboards/179

4) Vamos a Dashboard + Manage y hacemos un import.

Portainer
=========
```
root@master:~# docker volume create portainer_data
portainer_data

root@master:~# docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
Unable to find image 'portainer/portainer:latest' locally
latest: Pulling from portainer/portainer
d1e017099d17: Pull complete
d4e5419541f5: Pull complete
Digest: sha256:07c0e19e28e18414dd02c313c36b293758acf197d5af45077e3dd69c630e25cc
Status: Downloaded newer image for portainer/portainer:latest
b6b19712714471f67fd4dac85ac1467a003c806c7237d7e8409dad7b9bbd6d51
```

Finalmente:
```
root@master:~# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
redis                 latest              c188f257942c        6 days ago          94.9MB
grafana/grafana       latest              1e1c9863439f        9 days ago          236MB
prom/prometheus       latest              42e450d926a8        2 weeks ago         99.8MB
portainer/portainer   latest              00ead811e8ae        2 months ago        58.7MB
hello-world           latest              4ab4c602aa5e        2 months ago        1.84kB
google/cadvisor       latest              75f88e3ec333        11 months ago       62.2MB

root@master:~# docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                    NAMES
b6b197127144        portainer/portainer      "/portainer"             9 minutes ago       Up 9 minutes        0.0.0.0:9000->9000/tcp   trusting_johnson
fb0344239f35        grafana/grafana          "/run.sh"                21 minutes ago      Up 21 minutes       0.0.0.0:3000->3000/tcp   wizardly_chatterjee
38265ce3918d        prom/prometheus:latest   "/bin/prometheus --c…"   34 minutes ago      Up 25 minutes       0.0.0.0:9090->9090/tcp   prometheus
8482bfb097e1        google/cadvisor:latest   "/usr/bin/cadvisor -…"   34 minutes ago      Up 25 minutes       0.0.0.0:9091->8080/tcp   cadvisor
ddd30ff8b26c        redis:latest             "docker-entrypoint.s…"   34 minutes ago      Up 25 minutes       0.0.0.0:6379->6379/tcp   redis
```

Existen conflictos del puerto de portainer con el de sonarqube.
Hacemos un `docker ps -a` para ver los contenedores, con `docker rm` borraremos el contenedor que queramos y con el siguiente comando atacaremos desde navegador contra el puerto 9001 en lugar del 9000.
```
root@master:~#  docker run -d -p 9001:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

Ansible
=======

Instalando Ansible
------------------
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-machine

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt-get install ansible
```

Conectandonos con los nodos
---------------------------
Intentamos conectarnos con el resto de nodos pero no hemos hecho nada para conectarlos por lo que dará error.
```
vagrant@master:~$ ansible all -m ping
The authenticity of host '10.0.100.51 (10.0.100.51)' can't be established.
ECDSA key fingerprint is SHA256:iBWcGKKJv73115DfINEnuSq1c5iezlt2kf2wiCQGN/M.

Are you sure you want to continue connecting (yes/no)? yes
10.0.100.51 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added '10.0.100.51' (ECDSA) to the list of known hosts.\r\nPermission denied (publickey,password).\r\n",
    "unreachable": true
}
```

Generamos una clave publica y privada
```
vagrant@master:~/.ssh$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa.
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:6hNjXJMsH8diIjU+EoRAkPMFl2e0I3Z/g3PKivkqyG8 vagrant@master
The key's randomart image is:
+---[RSA 2048]----+
|=+.+oo.          |
|o ..+ =.         |
| o .o*+o o       |
|  ..oo=oO.o      |
|     + BS=+      |
|      =o.= .     |
|..   ..oo        |
|...E +..         |
|  oo+o+.         |
+----[SHA256]-----+
```

```
vagrant@master:~/.ssh$ ls -lt | more
total 12
-rw------- 1 vagrant vagrant 1675 Nov 22 14:52 id_rsa
-rw-r--r-- 1 vagrant vagrant  396 Nov 22 14:52 id_rsa.pub
```

La clave publica (1 linea) que hemos generado en el master la copiaremos en un fichero del nodo1 (que es con quien queremos contactar) y lo añadiremos a .ssh/authorized_keys concatenandola.

```
vagrant@node1:~/.ssh$ ls -lt id_rsa_master.pub
-rw-rw-r-- 1 vagrant vagrant 396 Nov 22 14:53 id_rsa_master.pub
vagrant@node1:~/.ssh$ ls -lt | more
total 16
-rw-rw-r-- 1 vagrant vagrant  396 Nov 22 14:53 id_rsa_master.pub
-rw------- 1 vagrant vagrant 1679 Nov 22 14:47 id_rsa
-rw-r--r-- 1 vagrant vagrant  395 Nov 22 14:47 id_rsa.pub
-rw------- 1 vagrant vagrant  389 Nov 22 12:25 authorized_keys
vagrant@node1:~/.ssh$ cat id_rsa_master.pub >> authorized_keys
vagrant@node1:~/.ssh$ cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/kWzjp4+SlyCR31e53tAAj4EB8pAceRND6v1i1KN5F5GV2Pwcnf2ZfrU3dbL8juJo1tiKGF6IkgZFjw+263S8jwt+xPnEl5BSrM9+rg0qENM+lINJWjaVCBQIe0dEeP9DB5azOLHE+vV2I3YzhyKATbgNJ8CxVGyw3F9vQ2Gl03O3OSzbBox8xYQV+nplAgCZ+gZxqPUWK3JTXdqBxCXJRWaUKF/7gjLLv9OlNnR1pvwGDiE22vKoj3GWaCHFBrZPaY1Ts6rmRjEl15ZGb/GTDOGbYDYl6NM1+ZMsDJv2i7kZG4vgStaOFgFexjGHVylC+WbXuyuORqSScTkzJoj3 vagrant
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDcxNx/x+2XzLOMjKZzF3+cKj8fR+0dDDRtqaFmLNbdPUEfUONSWaWuw+pBzenuGpKHbsQqOWwCLh8dwYKk0XrM0qMD2OQxcGzjZZ1KblLo+kCbIj9PHxLUHBX0s5goOEjNqTysd0Wuh24LLq4z86axUz4BjQIl+JWAHK8bvfsLoUibQj8b5ZkkCXx7QJAAWbEiagAnPOwd/GLkO4NFT1shxIc282d+8PcwbYIqa14V2IfkZXY5diW6jLvfFs/PBiM2HIIEmd7njnwvArrucSB1lfogL2M48G6MuwY4UMw3F8PIQoA9aLxBwVPsgJ/mK1uA/jd8hsfpcxdnOX0OiF5T vagrant@master
```

Ahora ya podemos acceder a los distintos nodos con los que hayamos hecho esto con ansible.

```
vagrant@master:~$ ansible all -m ping
The authenticity of host '10.0.100.51 (10.0.100.51)' can't be established.
ECDSA key fingerprint is SHA256:iBWcGKKJv73115DfINEnuSq1c5iezlt2kf2wiCQGN/M.
Are you sure you want to continue connecting (yes/no)? yes
10.0.100.51 | SUCCESS => {
    "changed": false,
    "ping": "pong"

}
```

En el fichero hosts tenemos la definicion de los hosts accesibles y los podemos agrupar.
```
vagrant@master:/etc/ansible$ more hosts
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

10.0.100.51

# Ex 1: Ungrouped hosts, specify before any group headers.

## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10

# Ex 2: A collection of hosts belonging to the 'webservers' group

## [webservers]
## alpha.example.org
## beta.example.org
## 192.168.1.100
## 192.168.1.110
```

Ejecutando sentencias remotas
------------------------------

Y a partir de ahi podemos lanzar ejecuciones remotas
```
vagrant@master:/etc/ansible$ ansible --inventory-file=/etc/ansible/hosts 10.0.100.51 -m command -a 'ls -lt'
10.0.100.51 | CHANGED | rc=0 >>
total 4
-rw-rw-r-- 1 vagrant vagrant 5 Nov 22 15:07 hola.txt


vagrant@master:/etc/ansible$ ansible all -m command -a 'ls -lt'
10.0.100.51 | CHANGED | rc=0 >>
total 4
-rw-rw-r-- 1 vagrant vagrant 5 Nov 22 15:07 hola.txt

vagrant@master:/etc/ansible$ ansible 10.0.100.51 -m command -a 'ls -lt'
10.0.100.51 | CHANGED | rc=0 >>
total 4
-rw-rw-r-- 1 vagrant vagrant 5 Nov 22 15:07 hola.txt


vagrant@node1:~$ ls -lt hola.txt
-rw-rw-r-- 1 vagrant vagrant 5 Nov 22 15:07 hola.txt
```

Sonarqube
=========

https://hub.docker.com/_/sonarqube/

```
root@master:~#  docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube

root@master:~# docker ps -a
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS                     PORTS                                            NAMES
20a3527243a3        sonarqube                "./bin/run.sh"           12 seconds ago      Up 10 seconds              0.0.0.0:9000->9000/tcp, 0.0.0.0:9092->9092/tcp   sonarqube
b6b197127144        portainer/portainer      "/portainer"             About an hour ago   Exited (2) 5 minutes ago                                                    trusting_johnson
fb0344239f35        grafana/grafana          "/run.sh"                About an hour ago   Up About an hour           0.0.0.0:3000->3000/tcp                           wizardly_chatterjee
38265ce3918d        prom/prometheus:latest   "/bin/prometheus --c…"   About an hour ago   Up About an hour           0.0.0.0:9090->9090/tcp                           prometheus
8482bfb097e1        google/cadvisor:latest   "/usr/bin/cadvisor -…"   About an hour ago   Up About an hour           0.0.0.0:9091->8080/tcp                           cadvisor
ddd30ff8b26c        redis:latest             "docker-entrypoint.s…"   About an hour ago   Up About an hour           0.0.0.0:6379->6379/tcp                           redis
f5ebfa18962f        hello-world              "/hello"                 2 hours ago         Exited (0) 2 hours ago                                                      vigorous_perlman
262e42b16922        hello-world              "/hello"                 2 hours ago         Exited (0) 2 hours ago                                                      xenodochial_hopper
37bd0a0d4399        hello-world              "/hello"                 4 weeks ago         Exited (0) 4 weeks ago                                                      awesome_sinoussi

```

Nginx
=====
https://hub.docker.com/_/nginx/
```
root@master:~/nginx1# cat index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>NGINX1</title>
</head>
<body>
    <h1>NGINX1</h1>
</body>
</html>
```

```
root@master:~/nginx1# docker run --name nginx1 -p 8081:80 -v "$PWD":/usr/share/nginx/html:ro -d nginx

root@master:~/nginx1# curl http://localhost:8081
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>NGINX1</title>
</head>
<body>
    <h1>NGINX1</h1>
</body>
</html>
```

Apache
======
https://hub.docker.com/_/httpd/

```
root@master:~/apache1# cat index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>APACHE1</title>
</head>
<body>
    <h1>APACHE1</h1>
</body>
</html>
```
```
root@master:~/apache1# docker run -dit --name apache1 -p 8082:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4

root@master:~/apache1# curl http://localhost:8082
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>APACHE1</title>
</head>
<body>
    <h1>APACHE1</h1>
</body>
</html>
```



Borrar todos los contenedores e imagenes
----------------------------------------
```
docker stop $(docker ps -a -q) & docker rm $(docker ps -a -q)
docker ps
docker images
```
