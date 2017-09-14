Nos bajamos el repo de composetest para probar docker compose con un ejemplo muy sencillo en python.

```
git clone https://github.com/netroby/composetest.git
```

Necesitara de estos elementos que se los bajara via pip
```bash
root@ubuntu:/home/ubuntu/misproyectos/composetest# more requirements.txt
flask
redis
```

Si vamos por proxy despues de requirements.txt pondriamos --proxy=http://USER:PASS@URL_PROXY:PORT_PROXY
en el Dockerfile directamente si no queremos paranoias con las variables de entorno y demás
```bash
root@ubuntu:/home/ubuntu/misproyectos/composetest# more Dockerfile
FROM python:2.7
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD python app.py
```

Esta es la aplicación python
```python
root@ubuntu:/home/ubuntu/misproyectos/composetest# more app.py
from flask import Flask
from redis import Redis
app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
        redis.incr('hits')
        return 'Hello world! I have been seens %s times.' % redis.get('hits')

if __name__ ==  "__main__":
        app.run(host="0.0.0.0", debug=True)
```

Y este el docker-compose YAML 
```
root@ubuntu:/home/ubuntu/misproyectos/composetest# more docker-compose.yml
version: '2'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
    depends_on:
     - redis
  redis:
    image: redis
```

Levantamos el contenedor
```
root@ubuntu:/home/ubuntu/misproyectos/composetest# docker-compose up
Creating composetest_redis_1 ...
Creating composetest_redis_1 ... done
Creating composetest_web_1 ...
Creating composetest_web_1 ... done
Attaching to composetest_redis_1, composetest_web_1
redis_1  | 1:C 14 Sep 18:32:01.781 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 14 Sep 18:32:01.781 # Redis version=4.0.1, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 14 Sep 18:32:01.781 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 14 Sep 18:32:01.785 * Running mode=standalone, port=6379.
redis_1  | 1:M 14 Sep 18:32:01.785 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 14 Sep 18:32:01.785 # Server initialized
redis_1  | 1:M 14 Sep 18:32:01.785 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1  | 1:M 14 Sep 18:32:01.785 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis_1  | 1:M 14 Sep 18:32:01.785 * Ready to accept connections
web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
web_1    |  * Restarting with stat
web_1    |  * Debugger is active!
web_1    |  * Debugger PIN: 163-430-268
```

Y en otra pantalla vemos la llamada HTTP.
Esta la podemos realizar desde aqui con curl, lynx o desde navegador con la ip que nos ha devuelto ifconfig si lo hacemos desde windows contra nuestro servidor ubuntu. Recordar tener habilitada la variable de entorno no_proxy si estamos trabajando con proxy, sino no funcionará directamente desde shell.

```
root@ubuntu:~# curl http://localhost:5000
Hello world! I have been seens 1 times

root@ubuntu:~# curl http://localhost:5000
Hello world! I have been seens 2 times

root@ubuntu:~# curl http://localhost:5000
Hello world! I have been seens 3 times

En el log ha aparecido

web_1    | 172.18.0.1 - - [14/Sep/2017 18:32:27] "GET / HTTP/1.1" 200 -
web_1    | 172.18.0.1 - - [14/Sep/2017 18:32:30] "GET / HTTP/1.1" 200 -
web_1    | 172.18.0.1 - - [14/Sep/2017 18:32:32] "GET / HTTP/1.1" 200 -

Comprobamos que ha pasado con docker
```bash
root@ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
composetest_web     latest              438329b80b88        19 minutes ago      685MB
<none>              <none>              6897b57fed3c        29 minutes ago      677MB
redis               latest              9813a7e8fcc0        39 hours ago        107MB
python              2.7                 5ca9b5ba555d        6 days ago          677MB

root@ubuntu:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
ac6b5b26fc86        composetest_web     "/bin/sh -c 'pytho..."   About a minute ago   Up About a minute   0.0.0.0:5000->5000/tcp   composetest_web_1
56934dc817fa        redis               "docker-entrypoint..."   About a minute ago   Up About a minute   6379/tcp                 composetest_redis_1
```
Hacemos un "Control+C" en la pantalla del servidor.

```bash
Gracefully stopping... (press Ctrl+C again to force)
Stopping composetest_web_1   ... done
Stopping composetest_redis_1 ... done
root@ubuntu:/home/ubuntu/misproyectos/composetest#

root@ubuntu:/home/ubuntu/misproyectos/composetest# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
root@ubuntu:/home/ubuntu/misproyectos/composetest# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
ac6b5b26fc86        composetest_web     "/bin/sh -c 'pytho..."   5 minutes ago       Exited (137) 16 seconds ago                       composetest_web_1
56934dc817fa        redis               "docker-entrypoint..."   5 minutes ago       Exited (0) 16 seconds ago                         composetest_redis_1
```
