

Documentación interesante en:
```
https://medium.com/@itsromiljain/docker-setup-and-dockerize-an-application-5c24a4c8b428
https://medium.com/@itsromiljain/dockerize-rest-spring-boot-application-with-hibernate-having-database-as-mysql-579abcc4edc4
https://medium.com/@itsromiljain/docker-compose-file-to-run-rest-spring-boot-application-container-and-mysql-container-775f15d21416
```

Jugando con una imagen de docker ya existente
---------------------------------------------

Tenemos solo una imagen bajada de docker, el `hello world` de la primera instalacion de docker.
```
root@ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              f2a91732366c        4 months ago        1.85kB
```
Sobre él podemos aplicarle un `tag`, que necesariamente tendrá que empezar por nuestro usuario de `Docker hub`.
Tambien podemos añadirle mas etiquetas como versionados y demas con ":".
```
root@ubuntu:~# docker tag f2a91732366c xpoveda/hello-world
root@ubuntu:~# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
hello-world           latest              f2a91732366c        4 months ago        1.85kB
xpoveda/hello-world   latest              f2a91732366c        4 months ago        1.85kB
```
Para poder hacer un `push`, es decir subir la imagen al registro de `Docker hub` lo que haremos antes de nada es hacer un `docker login`
```
root@ubuntu:~# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xpoveda
Password:
Login Succeeded
```
Y seguidamente el `push`
```
root@ubuntu:~# docker push xpoveda/hello-world
The push refers to repository [docker.io/xpoveda/hello-world]
f999ae22f308: Pushed
latest: digest: sha256:8072a54ebb3bc136150e2f2860f00a7bf45f13eeb917cca2430fcd0054c8e51b size: 524
```
Así tendremos la entrada en `https://hub.docker.com/r/xpoveda/hello-world/`



