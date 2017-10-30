
Super guía de Git y Github
==========================
```
* https://codigofacilito.com/cursos/git
* https://frontendlabs.io/3266--que-es-hacer-fork-repositorio-y-como-hacer-un-fork-github
* https://git-scm.com/book/es/v1
* http://rogerdudler.github.io/git-guide/index.es.html
```

Cheat sheet
-----------
```
* https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf
* https://education.github.com/git-cheat-sheet-education.pdf
* https://www.git-tower.com/blog/git-cheat-sheet
* http://rogerdudler.github.io/git-guide/files/git_cheat_sheet.pdf
```

Estados en Git
------------------
Estados:
  * Working: Modificar, crear y borrar archivos.    
  * Stage: Escoger cuales queremos subir.
  * Repository: Registrar los cambios en nuestro proyecto.

Configuración Git
------------------
Configuramos nuestro usuario con:
```
git config --global user.name "xpoveda"
git config --global user.mail "xpoveda@gmail.com"
git config --global color.vi true
```

Y con esto podemos revisar los parametros de configuracion
```
git config --global --list
git config --global user.name
```


Comandos principales
--------------------
```
git init                               Empezamos a monitorizar el proyecto

git status                             Estado de la sincronizacion con la "stage", asi como del repositorio local vs remoto. 
                                       Rojo pendiente de stage, verde en stage.

git add fichero                        Añadimos fichero a la "stage area".
git add -A                             Añadimos todos los ficheros al "stage area".

git commit -m "Descripcion del cambio" Hace commit de los cambios que habian subiddo a la "stage".

                                       En Git lo que almacenamos siempre es la foto justo de ese momento.
                                       Esa foto es apuntada por un hash como clave SHA que apuntara a todos los cambios que se han hecho
                                       a partir del commit justo anterior. 
                                    

git log                                Log de los commits. Se muestra la descripcion y SHA de cada uno de los commits de la rama actual.

git checkout X                         El checkout se situara en la rama X o en el commit con SHA X de la rama actual.

git reset X                            Elimina el commit con SHA X.
-- soft                                No toca el codigo
-- mixed                               Borra "stage" pero el area "work" la deja igual.
-- hard                                Borra todo, es decir SHA, cambios de la "work" hasta el commit anterior y cambios de la "stage".

git help                               Muestra los comandos posibles
git help comando                       Ayuda para el comando que estamos consultando
```


Ramas y fusiones
----------------
```
git branch                             Lista ramas.

git branch -a                          Lista tambien ramas ocultas.

git branch -v                          Lista el SHA y descripcion del HEAD de cada rama

git branch -D rama                     Borramos la rama

ubuntu@ubuntu:~/misproyectos/pruebasgit$ git branch -a
* master
  test
  remotes/origin/master
  remotes/origin/test

git branch test                        Crea la rama "test".
git checkout test                      Cambia a la rama "test". Se marca con un asterisco la rama actual.

git checkout -b test                   Creamos la rama "test" y nos movemos a ella.


git checkout master                    Nos situamos en la rama "master" con "checkout" y absorvemos los 
git merge test                         cambios de la rama "test" al hacer el "merge".

El merge puede ser:
* "fastforward"                        Se puede hacer si cambios en diferentes archivos o lineas de codigo.  
* "manual merge"                       Se puede hacer si cambios en iguales archivos o lineas de codigo.
```

Trabajando con Github
---------------------
Crearemos cuentas, abriremos proyectos privados o publicos.
Nos relacionaremos con nuestros seguidores, buscaremos proyectos.
Daremos a "star", "watch" y "forks".

Creamos un repo_local a partir de la url_git con `git clone http://url_git`

Pasos:

1) Creación del proyecto repo_remoto en Github. 
   Se asignará una url_git para ese repositorio

2) Opción A. Creamos un repo_local a partir de la url_git.
   La carpeta local se llamara igual que el repo.

```
git clone http://url_git
```
                                                    
3) Opción B. Creamos carpeta local, que puede ser diferente nombre del repo_remoto. Entramos en la carpeta y haremos el "init" y "remote". Conexion del repo_local con el repo_remoto.

```
git init y despues git remote add origin http://url_git
```

4) Se creará la rama "master" al hacer el primer commit de un fichero.

5) Vemos el estado de la vinculación entre los dos repos
```   
git remote -v
```

6) Eliminamos la conexion del repo_local con el repo_remoto
```
git remote remove origin
```

7) Cambios en los ficheros que queremos, añadimos en la "stage" y subimos a "repository"
```
git add -A
git commit -m "Descripcion de cambios"
```

8) Subimos los cambios al repo_remoto de la rama master
```
git push origin master
```


Comparación entre ramas y commits
---------------------------------
Despues de crear una rama test tendremos por ejemplo una rama "master" y otra "test".
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git branch
  master                                           
* test                
```

Nuestro repositorio apunta en este caso siempre remotamente "origin".
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git remote -v   
origin  https://github.com/xpoveda/pruebasgit.git (fetch)
origin  https://github.com/xpoveda/pruebasgit.git (push)   
```                           

Podemos hacer un diff de las ramas locales y las ramas remotas para ver la sincronizacion.
Aqui vemos que las dos ramas remotas son iguales.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff origin/master origin/test
ubuntu@ubuntu:~/misproyectos/pruebasgit$                                   
```

Pero que hemos modificado en local en la rama test un cambio.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff test origin/test
diff --git a/hola.txt b/hola.txt                                  
index 58b78cd..62ca9b9 100644                                     
--- a/hola.txt                                                    
+++ b/hola.txt                                                    
@@ -1 +1 @@                                                       
-paso1 modificacion en test                                       
+paso1                                                            
```

Lo vamos a subir a la rama test.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git push origin test
```

Y ahora vemos que estan sincronizadas.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff test origin/test
ubuntu@ubuntu:~/misproyectos/pruebasgit$                          
```

Tambien podemos ver las diferencias que hay entre dos commits 
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git log
commit 0a875c5547ccb9fb58fee54c2c8dd6ea54f012e7
Author: xpoveda <xpoveda@gmail.com>
Date:   Fri Sep 15 06:24:26 2017 -0700

    cambio en el hola.text para test

commit 87511e1edb1ccac288d0154102250a951fc66627
Author: xpoveda <xpoveda@gmail.com>
Date:   Fri Sep 15 06:13:38 2017 -0700

    Hemos hecho el cambio1 en hola.txt
```

```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff 87511e1edb1ccac288d0154102250a951fc66627 0a875c5547ccb9fb58fee54c2c8dd6ea54f012e7
diff --git a/hola.txt b/hola.txt                                                                                                   
index 62ca9b9..58b78cd 100644                                                                                                      
--- a/hola.txt                                                                                                                     
+++ b/hola.txt                                                                                                                     
@@ -1 +1 @@                                                                                                                        
-paso1                                                                                                                             
+paso1 modificacion en test                                                                                                        
```

Probando el merge de ramas
--------------------------
Haciendo mas pruebas hemos llegado al punto en que hemos hecho cambios en test que hemos subido, pero no estas sincronizado con la rama master.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff test master
diff --git a/hola.txt b/hola.txt
index 58b78cd..62ca9b9 100644
--- a/hola.txt
+++ b/hola.txt
@@ -1 +1 @@
-paso1 modificacion en test
+paso1
```

Hacemos un merge de lo que hay en test que se añade a master
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git checkout master
Switched to branch 'master'
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git merge test
Updating 87511e1..0a875c5
Fast-forward
 hola.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)


ubuntu@ubuntu:~/misproyectos/pruebasgit$ git diff test master
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git status
On branch master
nothing to commit, working directory clean
```

Y despues lo subimos al remoto.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git push origin master
Username for 'https://github.com': xpoveda
Password for 'https://xpoveda@github.com':
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/xpoveda/pruebasgit.git
   87511e1..0a875c5  master -> master
```

Vemos como se indica que se pasa de un estado X a otro Y.
```
ubuntu@ubuntu:~/misproyectos/pruebasgit$ git log
commit 0a875c5547ccb9fb58fee54c2c8dd6ea54f012e7
Author: xpoveda <xpoveda@gmail.com>
Date:   Fri Sep 15 06:24:26 2017 -0700

    cambio en el hola.text para test

commit 87511e1edb1ccac288d0154102250a951fc66627
Author: xpoveda <xpoveda@gmail.com>
Date:   Fri Sep 15 06:13:38 2017 -0700

    Hemos hecho el cambio1 en hola.txt
```


Github y control total sobre el ciclo de vida de los proyectos
--------------------------------------------------------------
Podemos ver código, issues, pull requests, wiki, pulse, graficos, settings, etc.
En settings por ejemplo podemos definir la rama por defecto.

* Issues: Correctivos + evolutivos + chat de discusion
* Milestones: Grupos de issues (optimizacion, fallos, etc), podemos indicar fecha fin opcionalmente. Agregamos las issues al milestone.
* Labels: Etiquetamos las issues.

Tambien podemos asignar las issues a ciertas personas.

* Tags: Puntos en la historia de los commits de nuestro proyecto.

```
git commit --amend -m ""      modificamos la descripcion del ultimo commit.
git push origin master -f     forzamos a realizar el commit aunque el unico cambio sea la descripcion.
git tag -a v1.0 -m "mensaje"  es un etiquetado anotado con descripcion del HEAD (o lo que es lo mismo, el ultimo commit).
git tag v1.0                  es la version ligera de anotacion.
git tag -a v0.9 -m "" X       anotamos (es decir etiquetamos con un cierto tag) el commit con SHA X.

git push origin v1.0          subimos el tag al repo_remoto que identifica un cierto commit.
                              Ese commit es una version, es decir un release que identificar un cierto estado del repositorio y 
                              no se refiere a ninguna rama.

git tag origin --tags         sube todos los tags.
```

Workflows
---------
Existen diferentes formas de trabajar:

1) Proyecto propio

2) Trabajo en grupo

3) Trabajo a tercero

   Máxima potencia de Github en mejora de código `Open source`.
   Por lo que otras personas nos tendran que aceptar los commits que queremos hacer.
   

Instrucciones que posibilitan todo esto son `git fetch` y `git merge`.

### Proyecto propio

Sin más. Intrucciones principales de Git.

### Trabajo en grupo

1) Creo una organizacion (comunidad) y le añado diferentes personas.
2) Podemos cambiar los privilegios sobre los repos que forman parte de la organizacion y
los privilegios de cada persona.

3) Transferimos repositorios a comunidad.
4) `git branch -a` muestra ramas ocultas.


```
            P1                                                        P2
                                                git remote add origin http://url_git_proyecto
                                                
                                                hago cambios
                                                git add -A
                                                git commit -m "cambio p2"
                                                
                                                git push origin master
                                                con esto subimos el cambio de mi repo_local al repo_remoto

git remote add origin http://url_git_proyecto

git fetch origin master
Nos descargamos los cambios que se han hecho en el remoto

hago cambios
git add -A
git commit -m "cambio p1"

git push origin master

si da error significa que no estas sincronizado, por lo que
te has de sincronizar tu rama master local con la remota origin/master
antes de nada haciendo un 

git merge origin/master 

para que se absorva.
```

Si se produce un conflicto Git te añadira textos como "<<<<< HEAD" en el codigo
y se tendra que hablar entre P1 y P2 para resolver el conflicto.


### Trabajo a tercero

Cuando queremos modificar proyectos de organizaciones de las que no formamos parte entonces
lo que haremos es un fork que lo hará es una copia del repositorio principal a un repositorio clon.

El repo_ppal sera la rama `upstream` y la clon la rama `origin`.

Generar carpeta del fork del repo_ppal.
```
git clone https://github.com/jansanchez/relay.git relay_forked
```
```
cd relay_forked
```

Creacion de rama del repo_original para podernos descargar las actualiaciones.
```
git remote add upstream https://github.com/facebook/relay.git
```

Actualizacion de nuestro upstream local con los cambios que han habido en upstream/master
```
git fetch upstream
```

Añadimos esas actualizaciones de upstream a nuestro master
```
git checkout master
git merge upstream/master
```

Y cuando hayamos hecho cambios lo que haremos es hacer esto para subirlo al origin/master
```
git push origin master
```

Esos cambios del origin/master son los que se pedira por pull request a la organizacion para que los añada.
