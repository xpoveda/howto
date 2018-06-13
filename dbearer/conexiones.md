Mysql as a service on Azure
===========================
Si estamos yendo contra mysql como servicio en Azure tenemos que habilitar en el portal azure que la conexion sea posible para
los servicios azure, la ip de nuestra maquina si estamos atacando en modo cliente sql y deshabilitar ssl.

Si accedemos via proxy cuidado con el puerto porque puede ser que este cortando el 3306. Probar con conexion directa (hotspot).

Postgress as a service on Heroku
================================
Para poder hacer las primeras pruebas hemos de poner los datos que se indican en el settings de la bbdd de heroku.
Seguidamente hay que añadir en el driver jdbc que dbearer genera automáticamente la propiedad sslmode=require en el 
apartado "Connection properties"
