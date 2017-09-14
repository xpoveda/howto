Borra todos los contenedores
```
docker rm -f $(docker ps -a -q)
```

Borra todas las imagenes
```
docker rmi -f $(docker images -q)
```

