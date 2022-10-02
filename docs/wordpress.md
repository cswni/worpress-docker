# Levantar un WordPress con Docker

Para crear un _blog_ con _WordPress_ necesitamos tener una base de datos dónde almacenar las entradas. Así que empezaremos creándola y después crearemos el contenedor de nuestro _blog_.

## Crear un contenedor con _MariaDB_.

_WordPress_ soporta los motores relaciones _MySQL_ y _MariaDB_. Usaremos este último.

!!! info "Crear el contenedor de MariaDB"
    CREAR LA CARPETA wordpressdb en la raiz de C
    
        docker run -d --name wordpress-db --mount type=bind,source=C:/wordpressdb,target=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=wordpress -e MYSQL_USER=manager -e MYSQL_PASSWORD=secret mariadb

La imagen se descargará, si no lo estaba ya, y se iniciará nuestro contenedor de _MariaDB

El principal cambio en `docker run` con respecto a la última vez es que no hemos usado
`-p` (el parámetro para publicar puertos) y hemos añadido el parámetro `-d`.

Lo primero que habremos notado es que el contenedor ya no se queda en primer plano. El parámetro `-d` indica que debe ejecutarse como un proceso en segundo plano. Así no podremos pararlo por accidente con `Control+C`.

Lo segundo es que vemos que el contenedor usa un puerto, el `3306/tcp`, pero no está linkado a la máquina anfitrión. No tenemos forma de acceder a la base de datos directamente. Nuestra intención es que solo el contenedor de _WordPress_ pueda acceder.

Luego una serie de parámetros `-e` que nos permite configurar nuestra base de datos.

!!! info
    Los contenedores se configuran a través de variables de ambiente, que podemos configurar con el parámetro `-e` que vemos en la orden anterior. Gracias a ellos hemos creado una base de datos, un usuario y configurado las contraseñas.

    Se recomienda buscar en el registro de _Docker_ la imagen oficial de [MariaDB](https://hub.docker.com/_/mariadb/) para entender el uso de los parámetros.

Por último, el parámetro `--mount` nos permite enlazar el volumen que creamos en el paso anterior con el directorio `/var/lib/mysql` del contenedor. Ese directorio es donde se guardan los datos de _MariaDB_. Eso significa que si borramos el contenedor, o actualizamos el contenedor a una nueva versión, no perderemos los datos porque ya no se encuentran en él, si no en el volumen. Solo lo perderíamos si borramos explícitamente el volumen.

!!! warning
    Cada contendor que usemos tendrá uno o varios directorios donde se deben guardar los datos no volátiles. Nos corresponde a nosotros conocer la herramienta y saber de qué directorios se tratan. Usualmente están en la documentación del contenedor, pero no siempre.

!!! info
    El parámetro `--mount` se empezó a utilizar desde la versión `17.06` para contenedores independientes (los que no pertenecen a un enjambre o _swarm_). Los que conozcan _Docker_ de versiones más antiguas estarán más acostumbrados a usar el parámetro `--volume` que hace algo similar. Sin embargo la documentación aconseja usar ya `--mount`, sobre todo para nuevos usuarios.

    Nosotros somos muy obedientes así que en este taller usaremos `--mount`.

## Creando nuestro proyecto con WordPress

Vamos a crear otra vez nuestro contenedor de _WordPress_, pero esta vez vamos a conectarlo con nuestra base de datos. Además, queremos poder editar los ficheros de las plantillas, por si tenemos que modificar algo, así que necesitaremos montar el directorio del contenedor donde está instalado _WordPress_ con nuestra cuenta de usuario en la máquina anfitrión.

!!! info "Crear la carpeta en C"
    Vamos a crear una carpeta en la raiz de C:

        Usar el explorador de windows y crearla con el nombre 
        
        wordpress

!!! info "Crear el contenedor de Wordpress"
    Y dentro de este directorio arrancamos el contenedor:

        docker run -d --name wordpress 
            --link wordpress-db:mysql 
            --mount type=bind,source=C:/wordpress,target=/var/www/html 
            -e WORDPRESS_DB_USER=manager 
            -e WORDPRESS_DB_PASSWORD=secret 
            -p 8080:80 
            wordpress

 > Este proceso descarga unos 200 mb aprox

Cuando termine la ejecución, si accedemos a la dirección [http://localhost:8080/](http://localhost:8080/), ahora sí podremos acabar el proceso de instalación de nuestro WordPress. Podemos revisar el directorio c:/wordpress y ahi veremos el listado de todos los archivos de worpress.

!!! error "IMPORTANTE"
    IMPORTANTE:

    1. Despues de la descarga de la imagen de Worpress cuando accedemos a la dirección [http://localhost:8080/](http://localhost:8080/) puede que no les cargue de forma inmediata. Deben esperar que se inicien los servicios que requiere Worpress para trabajar.


## Instalacion de Wordpress

 * Elegir el idioma: Español 
 * Titulo del sitio: Mi Sitio Web (Pueden cambiar al que deseen)
 * Nombre de usuario: admin
 * Contraseña: Worpress genera una pero la pueden cambiar. Anotenla
 * Correo electronico. Usen el educativo
 * Visibilidad de los motores de busqueda. Activado
 * Instalar WordPress
 * Lo lograstes. Clic en Acceder y usaan el usuario


!!! info "INFO"
    Detalles:

    1. Acceder a la ruta [http://localhost:8080/](http://localhost:8080/) para revisar el sitio web.