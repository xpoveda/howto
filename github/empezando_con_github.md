Empezando a trabajar con Github
===============================

1) Crearemos un repositorio en Github

2) Creamos un directorio local con el mismo nombre que el repositorio
~~~ bash
ubuntu@ubuntu:~/misproyectos$ mkdir howto
ubuntu@ubuntu:~/misproyectos$ cd howto
~~~

3) Inicializamos Git sobre él realizando un primer commit 
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto$ git init
Initialized empty Git repository in /home/ubuntu/misproyectos/howto/.git/
ubuntu@ubuntu:~/misproyectos/howto$ echo "hola" > README.md
ubuntu@ubuntu:~/misproyectos/howto$ git add --all
ubuntu@ubuntu:~/misproyectos/howto$ git commit -m "v1"
[master (root-commit) f16b64e] v1
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
 ~~~

4) Le asignamos la rama "master" al repo
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto$ git remote add origin https://github.com/xpoveda/howto.git
~~~ 

5) Subimos los cambios
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto$ git push -u origin master
Username for 'https://github.com': xpoveda
Password for 'https://xpoveda@github.com':
Counting objects: 3, done.
Writing objects: 100% (3/3), 206 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/xpoveda/howto.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
~~~ 

6) Para cualquier cambio que realicemos sobre los ficheros podemos aplicar las siguientes instrucciones enlazadas.

Con esto lo que haremos es añadir todos los ficheros al control de versiones (por si hubiera alguno nuevo), aplicariamos un 
commit sobre los que esten modificados/nuevos/borrados y lo subiriamos a la rama "master" de Github.
~~~ bash
git add --all && git commit -m "modificacion" && git push -u origin master
~~~ 

7) Aquí vemos un ejemplo de como queda..
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto/github$ vi empezando_con_github.md
ubuntu@ubuntu:~/misproyectos/howto/github$ cd ..
ubuntu@ubuntu:~/misproyectos/howto$ git add --all && git commit -m "modificacion" && git push -u origin master
[master 2394f20] modificacion
 3 files changed, 41 insertions(+), 1 deletion(-)
 create mode 100644 github/empezando_con_github.md
 create mode 100644 github/hola.txt
Username for 'https://github.com': xpoveda
Password for 'https://xpoveda@github.com':
Counting objects: 5, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 1.09 KiB | 0 bytes/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To https://github.com/xpoveda/howto.git
   f16b64e..2394f20  master -> master
Branch master set up to track remote branch master from origin.
~~~ 

8) Si realizamos modificaciones directas sobre el "master" y despues queremos actualizar el local
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto$ git pull origin master
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
From https://github.com/xpoveda/howto
 * branch            master     -> FETCH_HEAD
   7c43350..5e4a410  master     -> origin/master
Updating 7c43350..5e4a410
Fast-forward
 github/empezando_con_github.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~

Y cuando ya estamos actualizados..
~~~ bash
ubuntu@ubuntu:~/misproyectos/howto$ git pull origin master
From https://github.com/xpoveda/howto
 * branch            master     -> FETCH_HEAD
Already up-to-date.
~~~ 
