Entorno de desarrollo en local
==============================

Prometheus
-----------

https://prometheus.io/docs/prometheus/latest/installation/

```
docker pull prom/prometheus
```

Cadvisor
--------

https://prometheus.io/docs/guides/cadvisor/

```
root@master:~/proyectos/cadvisor# cat prometheus.yml
scrape_configs:
- job_name: cadvisor
  scrape_interval: 5s
  static_configs:
  - targets:
    - cadvisor:8080
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
    - 8080:8080
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


Instamos grafana
----------------
```
root@master:~# docker run -d -p 3000:3000 grafana/grafana
```

1) Accedemos a grafana por la IP y el puerto 3000. El usuario sera admin/admin y despues lo modificaremos.

2) Vamos a Confiuration + Data Sources y hacemos un añadir Prometheus con nombre Prometheus accediendo por la IP donde tenemos instalado Prometheus y el puerto 9090

3) Extraer el json para hacer dashboard https://grafana.com/dashboards/179

4) Vamos a Dashboard + Manage y hacemos un import.

Portainer
---------
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
8482bfb097e1        google/cadvisor:latest   "/usr/bin/cadvisor -…"   34 minutes ago      Up 25 minutes       0.0.0.0:8080->8080/tcp   cadvisor
ddd30ff8b26c        redis:latest             "docker-entrypoint.s…"   34 minutes ago      Up 25 minutes       0.0.0.0:6379->6379/tcp   redis
```

Ansible
-------
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-machine

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt-get install ansible
```

Sonarqube
---------

https://hub.docker.com/_/sonarqube/

```
root@master:~#  docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube

root@master:~# docker ps -a
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS                     PORTS                                            NAMES
20a3527243a3        sonarqube                "./bin/run.sh"           12 seconds ago      Up 10 seconds              0.0.0.0:9000->9000/tcp, 0.0.0.0:9092->9092/tcp   sonarqube
b6b197127144        portainer/portainer      "/portainer"             About an hour ago   Exited (2) 5 minutes ago                                                    trusting_johnson
fb0344239f35        grafana/grafana          "/run.sh"                About an hour ago   Up About an hour           0.0.0.0:3000->3000/tcp                           wizardly_chatterjee
38265ce3918d        prom/prometheus:latest   "/bin/prometheus --c…"   About an hour ago   Up About an hour           0.0.0.0:9090->9090/tcp                           prometheus
8482bfb097e1        google/cadvisor:latest   "/usr/bin/cadvisor -…"   About an hour ago   Up About an hour           0.0.0.0:8080->8080/tcp                           cadvisor
ddd30ff8b26c        redis:latest             "docker-entrypoint.s…"   About an hour ago   Up About an hour           0.0.0.0:6379->6379/tcp                           redis
f5ebfa18962f        hello-world              "/hello"                 2 hours ago         Exited (0) 2 hours ago                                                      vigorous_perlman
262e42b16922        hello-world              "/hello"                 2 hours ago         Exited (0) 2 hours ago                                                      xenodochial_hopper
37bd0a0d4399        hello-world              "/hello"                 4 weeks ago         Exited (0) 4 weeks ago                                                      awesome_sinoussi

```

Borrar todos los contenedores e imagenes
----------------------------------------
```
docker stop $(docker ps -a -q) & docker rm $(docker ps -a -q)
docker ps
docker images
```
