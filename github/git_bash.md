http://rogerdudler.github.io/git-guide/index.es.html

http://rogerdudler.github.io/git-guide/files/git_cheat_sheet.pdf


Desde `git bash` de windows podemos trabajar tambien con git para acceder a nuestros repositorios remotos o incluso trabajar graficamente 
con `git gui`

Para ello creamos un repositorio publico en `github` y a continuacion una carpeta en local con el mismo nombre donde tendremos
los fuentes de nuestro proyecto.

```
xpoveda@XPOVEDA01W7 MINGW64 /c/spartseo/openwebinars_examples
$ git status
fatal: Not a git repository (or any of the parent directories): .git
```

Inicializamos git en la carpeta local

```
echo "# openwebinars_examples" >> README.md
git init
```

Configuramos nuestro usuario asi como la informacion del proxy si lo tuvieramos
```
git config --global user.email "xpoveda@gmail.com"
git config --global user.name "xpoveda"
git config --global http.proxy http://USUARIO:PASSWORD@SERVIDOR:PUERTO
```

Añadimos los ficheros (que seran todos inicialmente y le hacemos un commit)
```
git add --all
git commit -m "first commit"
```

Apuntamos hacia la rama remota
```
git remote add origin https://github.com/xpoveda/openwebinars_examples.git
```

Y le hacemos un *push*
```
git push -u origin master
```

```
$ git push -u origin master
Counting objects: 8826, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4211/4211), done.
Writing objects: 100% (8826/8826), 94.63 MiB | 87.14 MiB/s, done.
Total 8826 (delta 2601), reused 8822 (delta 2600)
remote: Resolving deltas: 100% (2601/2601), done.
To https://github.com/xpoveda/openwebinars_examples.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```
```
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/xpoveda/openwebinars_examples.git
  Push  URL: https://github.com/xpoveda/openwebinars_examples.git
  HEAD branch: master
  Remote branch:
    master tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
 ```
 
