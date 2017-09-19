
Lanzando proyectos Spring en UNIX y Windows
===========================================
Lanzar proyectos spring desde **UNIX** es muy sencillo.
Simplemente se ha de bajar el proyecto de Github, regenerarlo con Maven o Gradle y lanzarlo tal y como se indica en las instrucciones.

Lo mismo se puede hacer tambien desde el editor *STS-Spring tool suite* en **Windows** muy facilmente.
Estos pasos aplican tanto a proyectos Spring como No-Spring.

En el editor iremos a `Window` despues a `Show view` y buscaremos `Git repositories`.
Con esto tendremos una ventana en la que podremos ver la vista que tenemos en Github. 

Ahi le daremos al `Clone a git repository..`, despues a `Github` y `Next`.
Despues pondremos el nombre del proyecto y nos apareceran los proyectos que ha hospedados ahi, tambien los forks.
Lo seleccionaremos y STS nos lo bajará a un directorio local de nuestro windows.

Si buscamos asi los proyectos llegará un momento en el que el API de Github saltará por maximo número de consultas.
La forma directa es ir por `Clone a git repository..`, despues a `Clone Uri` y despues a `Next`.
Ahi le añadiremos la URI de git, el nos añadira el host (github.com) y el path del repositorio.
Nos autenticamos con el usuario y password de Github y ya esta listo para bajarse ese repositorio a nuestro local de Windows.

Despues nos situaremos con el boton derecho en el nodo principal del proyecto que nos queremos bajar y le daremos a `Properties`.
Con eso lo que haremos es localizar el directorio donde STS se ha bajado los fuentes de Github.

Seguidamente haremos un `File` y un `Open file from file system`, indicando a continuacion el directorio local donde esta el proyecto.

Con todo esto lo que conseguimos es bajarnos el proyecto, poderlo modificar, regenerar y probar desde windows.

Versionado en STS con Git directamente con la opcion "Team"
------------------------------------------------------------
En STS podremos abrir un terminal del tipo "Git bash" y tendremos sentencias parecidas a UNIX.

```
pwd
cd /c/Users/xpoveda/git/maven-hello-world/my-app
```

```
$ git remote -v

origin  https://github.com/xpoveda/maven-hello-world.git (fetch)
origin  https://github.com/xpoveda/maven-hello-world.git (push)
```

El directorio que nos hemos bajado en local en un workspace tambien apunta al mismo directorio por lo que 
los ficheros estan sincronizados.

Podemos revisar el status del versionado de nuestro proyecto mediante la instruccion git o mediante el boton derecho "Team".

Con `Team` podremos hacer todo el tratamiento de versionado de una forma muy sencilla.

Podremos hacer por ejemplo un "Commit" y ver que hay en la "stage area"y que no.
Y tambien hacer "drag and drop" de los ficheros que pasen a la "stage area" para despues hacer un commit y un push.

Podremos crear nuevas ramas, hacer "merge", cambiar entre ellas e incluso hacer un "pull request" o una subida al "upstream" del repositorio principal si este venia de un forked.

Tambien podemos hacer comparaciones con el "HEAD".

**Increible!!** 

Las guias
---------
Spring guides
```
http://spring.io/guides
```

Building rest services with spring
```
http://spring.io/guides/tutorials/bookmarks/
```

Spring boot with docker
```
http://spring.io/guides/gs/spring-boot-docker/
```

forks de repositorios de spring
```
https://github.com/xpoveda/gs-spring-boot
https://github.com/xpoveda/gs-spring-boot-docker
https://github.com/xpoveda/gs-rest-service
https://github.com/xpoveda/tut-bookmarks
```




