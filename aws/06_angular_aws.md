Angular AWS (y un poquito de springboot)
========================================

Generación de los módulos a subir a producción
----------------------------------------------
Generamos la aplicación mediante `ng build --env=prod` en lugar de `ng build --prod`.

Si lo hacemos con en `--prod` da error al realizarse la transpilación.
Este no es un problema que se da cuando se ejecuta en modo desarrollo ya que se usa `npm start` (con el proxy) o un `ng serve` (sin).

Copia de la carpeta dist
------------------------
Copiamos el contenido de la carpeta `dist` al `DocumentRoot` que marcamos en el fichero `.conf` de Apache.
En nuestro caso hemos creado una carpeta `angular.xavierpoveda.com` que cuelga de `var/www` y referenciaremos la aplicacion mediante un subdominio.

.htaccess
----------
Creamos tambien un fichero `.htaccess` en ese directorio raiz con este contenido
```
ubuntu@ip-172-31-33-239:/var/www/angular.xavierpoveda.com$ cat .htaccess
<IfModule mod_rewrite.c>
    RewriteEngine on

    # Don't rewrite files or directories
    RewriteCond %{REQUEST_FILENAME} -f [OR]
    RewriteCond %{REQUEST_FILENAME} -d
    RewriteRule ^ - [L]

    # Rewrite everything else to index.html
    # to allow html5 state links
    RewriteRule ^ index.html [L]
</IfModule>
```

Ruta de la API en el servicio de configuración
-----------------------------------------------
El fichero `proxy.conf.json` que utilizabamos para configurar un proxy en desarrollo y poder apuntar a un API de pruebas que estuviera
en nuestra misma máquina no nos sirve para produccion.
```
{
    "/api": {
    "target": "http://localhost:8080",
    "secure": false
    }
}
```

Ahora en el servicio de configuración tendremos que poner en lugar de la ruta relativa

```
@Injectable()
export class ApiUrlConfigService {

  public _loginURL           = '/api/login';
  public _whoamiURL          = '/api/whoami';
  public _refreshURL         = '/api/refresh';
```
la ruta absoluta 

```
@Injectable()
export class ApiUrlConfigService {

  public _loginURL           = 'https://api.xavierpoveda.com/api/login';
  public _whoamiURL          = 'https://api.xavierpoveda.com/api/whoami';
  public _refreshURL         = 'https://api.xavierpoveda.com/api/refresh';
```

Habilitar CORS del API
----------------------
Modificamos el fichero `WebSecurityConfig.java` de `springboot` para permitir las llamadas al API desde ANGULAR y que el navegador no 
devuelva error de CORS.

En nuestro caso solo permitimos llamadas desde  `https` y desde la URL `angular.xavierpoveda.com`.
Podemos añadir tanto origines como llamadas a `config.addAllowedOrigin` hagamos.

```
    @Bean
    public FilterRegistrationBean corsFilter() {
            UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
            CorsConfiguration config = new CorsConfiguration();
            config.setAllowCredentials(true);
            
            //config.addAllowedOrigin("http://angular.xavierpoveda.com");
            config.addAllowedOrigin("https://angular.xavierpoveda.com");
            
            config.addAllowedHeader("*");
            config.addAllowedMethod("*");
            source.registerCorsConfiguration("/**", config);
            FilterRegistrationBean bean = new FilterRegistrationBean(new CorsFilter(source));
            bean.setOrder(0);
            return bean;
    }
```
