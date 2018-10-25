
Multi-máquina con java, maven y docker
======================================
Esta es la configuracion minima para poder trabajar con java, maven y docker.

Si estamos trabajando con vmware tenemos que habilitar el `ssh`. Si lo hacemos con vagrant no hace falta ya que siempre se va contra localhost
y los puertos estan redirigidos en la maquina host (windows) para que vayan al 22 de cada maquina guest (linux).
```
sudo apt-get install openssh-server
sudo service ssh status
```
Instalamos `java 8`
```
sudo apt-get install openjdk-8*
```

Instalación de la última versión de `docker`
```
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
Y de `docker compose`
```
sudo -i
## revisar si es la ultima version con https://docs.docker.com/compose/install/
sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
Con `maven`  ya tenemos un primer paquete para poder trabajar.
```
sudo apt-get install maven
```
