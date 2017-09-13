1) Creamos un repositorio en Github

2) Creamos un directorio local con el mismo nombre que el repositorio

ubuntu@ubuntu:~/misproyectos$ mkdir howto
ubuntu@ubuntu:~/misproyectos$ cd howto

3) Inicializamos Git sobre él realizando un primer commit 

ubuntu@ubuntu:~/misproyectos/howto$ git init
Initialized empty Git repository in /home/ubuntu/misproyectos/howto/.git/
ubuntu@ubuntu:~/misproyectos/howto$ echo "hola" > README.md
ubuntu@ubuntu:~/misproyectos/howto$ git add --all
ubuntu@ubuntu:~/misproyectos/howto$ git commit -m "v1"
[master (root-commit) f16b64e] v1
 1 file changed, 1 insertion(+)
 create mode 100644 README.md

4) Le asignamos la rama "master" al repo
ubuntu@ubuntu:~/misproyectos/howto$ git remote add origin https://github.com/xpoveda/howto.git

5) Subimos los cambios

ubuntu@ubuntu:~/misproyectos/howto$ git push -u origin master
Username for 'https://github.com': xpoveda
Password for 'https://xpoveda@github.com':
Counting objects: 3, done.
Writing objects: 100% (3/3), 206 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/xpoveda/howto.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.

6) Para cualquier cambio que realicemos sobre los fichero podemos aplicar las siguientes instrucciones enlazadas.

Con esto lo que haremos es añadir todos los ficheros al control de versiones (por si hubiera alguno nuevo), aplicariamos un 
commit sobre los que esten modificados/nuevos/borrados y lo subiriamos a la rama "master" de Github.

git add --all && git commit -m "modificacion" && git push -u origin master
