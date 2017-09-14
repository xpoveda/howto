Borra todos los contenedores
```
docker rm -f $(docker ps -a -q)
```

Borra todas las imagenes
```
docker rmi -f $(docker images -q)
```


Utilidades para hacer espacio en docker cuando nos dice que esta al 100% pero no es asi

```
root@ubuntu:/var/lib/docker/aufs/diff# df -a | grep docker
df: /mnt/hgfs: Protocol error
/dev/sda1       19478204 8799148   9666576  48% /var/lib/docker/plugins
/dev/sda1       19478204 8799148   9666576  48% /var/lib/docker/aufs
````

```
docker volume rm $(docker volume ls -qf dangling=true)
```

```
root@ubuntu:/var/lib/docker/aufs/diff# docker system prune
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache
Are you sure you want to continue? [y/N]
```

