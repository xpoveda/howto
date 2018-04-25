SPRINGBOOT AWS
==============
https://www.digitalocean.com/community/tutorials/how-to-install-the-latest-mysql-on-ubuntu-16-04
https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-16-04

http://xavierpoveda.com/phpmyadmin/

Creamos usuario y bbdd asociada "test-oapc".
```
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ mvn clean
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ mvn install
ubuntu@ip-172-31-33-239:~/misproyectos/oapc-server$ java -jar ./target/spring-boot-oapc-0.1.0-SNAPSHOT.jar


```
sudo a2enmod headers
sudo service apache restart
```

```
http://xavierpoveda.com:8090/api/login
http://api.xavierpoveda.com/api/login
https://api.xavierpoveda.com/api/login
Content-Type:application/json
Body raw application/json
{"username":"admin","password":"123"}
```

Respuesta:
```
{
    "access_token": "eyJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzcHJpbmdib290LW9hcGMtc2VydmVyIiwic3ViIjoiYWRtaW4iLCJhdWQiOiJ3ZWIiLCJpYXQiOjE1MjQ2NDk2NzYsImV4cCI6MTUyNDY1MDI3Nn0.Nu2ESXxrOJmIORJORkGQpc33VJI0j1JN5Q0FFOZkyXy1mE4nlpZPcOkjdTRUzSJZg4qv9RXdsSQExMGhNqW0Aw",
    "expires_in": 600
}
```

```
https://api.xavierpoveda.com/api/whoami
Content-Type:application/json
Authorization:Bearer eyJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzcHJpbmdib290LW9hcGMtc2VydmVyIiwic3ViIjoiYWRtaW4iLCJhdWQiOiJ3ZWIiLCJpYXQiOjE1MjQ2NDkzMTYsImV4cCI6MTUyNDY0OTkxNn0.ujZy-wdhcn8_4VVPhjTagpcwnYVWxOYG2Ek85cOIuYUQcTp_WEKa3qNJ51Xij55aM3OHVtOyucNLAWNSvIAbJA



