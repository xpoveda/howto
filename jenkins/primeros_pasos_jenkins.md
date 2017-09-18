Primeros pasos con jenkins
===========================

https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Ubuntu

Instalando Jenkins
------------------
Primero de todo lo instalamos, tambien existe  una versión dockerizada que en este caso no utilizaremos.
```bash
ubuntu@ubuntu:~$ wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
ubuntu@ubuntu:~$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
ubuntu@ubuntu:~$ sudo apt-get update
ubuntu@ubuntu:~$ sudo apt-get install jenkins
```

Modificamos los puertos por defecto en /etc/default/jenkins cambiando el 8080 por 8099.
```bash
ubuntu@ubuntu:~$ sudo /etc/init.d/jenkins restart
```

El acceso será mediante `http://my_ip:8099`

Aquí tendremos la contraseña que nos dara jenkins.
```bash
ubuntu@ubuntu:~$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
0ca63ef04b2f4351957d34a174f0b2e0
```

Instalamos los plugins sugerido
```bash
Install suggested plugins
```

Trabajando con Jenkins
-----------------------
Una vez en Jenkins podemos crear una tarea.

1) Le damos un nombre + `Crear proyecto estilo libre` + **OK**.
2) En `Configurar el origen del código fuente` ponemos **Git** y la URL del repositorio, por ejemplo *https://github.com/xpoveda/maven-hello-world.git*
3) Añadimos tambien las credenciales de "Git", el "usuario" y su "contraseña" + **Add**.
4) En `Ejecutar` podemos poner varios pasos, uno por ejemplo puede ser `Ejecutar tareas maven de nivel superior`, despues ponemos **clean package** por ejemplo
y despues en `Avanzado` pondriamos donde esta el pom.xml que en nuestro caso está en **my-app/pom.xml**

Con la tarea ya creada iremos a `Construir ahora` del menu principal y lo ejecutaremos.

Si todo esta correcto nos dara bien, siempre podemos ver en la `Zona de trabajo`.
Ahi podemos ver los fuentes directamente y los logs de las distintas ejecuciones en `Console output`.

La ejecucion de trabajos, su integracion con github y el paso a paso que se puede realizar lanzando diversas opciones es la mayor potencia de Jenkins.
