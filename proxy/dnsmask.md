
# Modificando DNSMASK y configuración de docker para docker-compose con proxy

## Tocamos DNSMASK
Realizando pruebas vemos que hay veces que en la creación de los contenedores con docker-compose a veces falla por un tema de DNS.

Para resolverlo una solución (no aplicable a cualquier problema pero nos sirve para practicar) es modificar el dnsmask

Simplemente modificamos el fichero `/etc/NetworkManager/NetworkManager.conf` comentando la linea que pone `dns=dnsmasq`
Y hacemos un `service network-manager restart`.

## Y ahora docker

Extraemos el DNS de nuestro sistema.
```
root@ubuntu:~# nmcli dev show | grep 'IP4.DNS'
IP4.DNS[1]:                             192.168.140.2
```

Y modificamos el fichero de configuracion de docker.

Este es el original
```
root@ubuntu:/home/ubuntu/misproyectos/composetest# more /etc/default/docker
# Docker Upstart and SysVinit configuration file

#
# THIS FILE DOES NOT APPLY TO SYSTEMD
#
#   Please see the documentation for "systemd drop-ins":
#   https://docs.docker.com/engine/admin/systemd/
#

# Customize location of Docker binary (especially for development testing).
#DOCKERD="/usr/local/bin/dockerd"

# Use DOCKER_OPTS to modify the daemon startup options.
#DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4"

# If you need Docker to use an HTTP proxy, it can also be specified here.
#export http_proxy="http://127.0.0.1:3128/"

# This is also a handy place to tweak where Docker's temporary files go.
#export DOCKER_TMPDIR="/mnt/bigdrive/docker-tmp"
```

Y este el nuevo
```
root@ubuntu:/home/ubuntu/misproyectos/composetest# more /etc/default/docker
# Docker Upstart and SysVinit configuration file

#
# THIS FILE DOES NOT APPLY TO SYSTEMD
#
#   Please see the documentation for "systemd drop-ins":
#   https://docs.docker.com/engine/admin/systemd/
#

# Customize location of Docker binary (especially for development testing).
#DOCKERD="/usr/local/bin/dockerd"

# Use DOCKER_OPTS to modify the daemon startup options.
DOCKER_OPTS="--dns 192.168.140.2 --dns 8.8.8.8 --dns 8.8.4.4"

# If you need Docker to use an HTTP proxy, it can also be specified here.
export http_proxy="http://USER:PASS@URL_PROXY:URL_PORT"
export HTTP_PROXY="http://USER:PASS@URL_PROXY:URL_PORT"

# This is also a handy place to tweak where Docker's temporary files go.
#export DOCKER_TMPDIR="/mnt/bigdrive/docker-tmp"
```

Y reiniciamos docker
```
service docker stop && service docker start
```
