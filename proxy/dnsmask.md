Realizando pruebas vemos que hay veces que en la creación de los contenedores con docker-compose a veces falla por un tema de DNS.

Para resolverlo una solución (no aplicable a cualquier problema pero nos sirve para practicar) es modificar el dnsmask

Simplemente modificamos el fichero `/etc/NetworkManager/NetworkManager.conf` comentando la linea que pone `dns=dnsmasq`
Y hacemos un `service network-manager restart`.

