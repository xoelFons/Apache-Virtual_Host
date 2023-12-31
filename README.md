# **Parte 1: _Docker compose_**


Para el docker compose, usaremos de plantilla uno que ya tengamos hecho, este docker compose debe tener un servidor apache, un servidor DNS y un cliente para hacer las pruebas.


El docker compose debe quedar algo así: 

**_services:_**

  **servidor_apache:**

    container_name: asir_apache_web

    image: httpd:latest

    ports:

      - "80:80"

    # Mapeamos el directorio raíz (equipo:contenedor)  

    volumes:

      - ./hello_wld:/usr/local/apache2/htdocs

      - ./conf:/usr/local/apache2/conf   

    networks:

      red_33:

        ipv4_address: 33.33.5.14

  **servidor_dns:**

    container_name: asir_servidor_dns

    image: ubuntu/bind9

    ports:

      - "53:53/tcp"

      - "53:53/udp"

      #Mapeo de puertos

    networks:

      red_33:

        ipv4_address: 33.33.5.1

    volumes:

      - ./configuration:/etc/bind

      - ./zones:/var/lib/bind

      #Para mapear los directorios

  **cliente:**
  

    container_name: asir_cliente_web

    image: alpine

    platform: linux/amd64

    tty: true

    stdin_open: true

    dns:

      - 33.33.5.1

    networks:

      red_33:

        ipv4_address: 33.33.5.33


**networks:**

  **red_33:**

    external: true




# **Parte 2: _Databases_**


Despues de tener ya una red creada y conectada para esta práctica, vamos a crear y vincular unas bases de datos con el servidor apache, con la finalidad de que nos lo proporcione el servidor DNS.

Va a haber 2 databases: _fabulaoscura_ y _fabulamaravillosa_, pero solo pondré una de ellas, ya que es la misma db con distintos nombres.


$TTL 38400	; 10 hours 40 minutes

@		IN SOA	ns.fabulamaravillosa.int. some.email.address. (

				10000002   ; serial

				10800      ; refresh (3 hours)

				3600       ; retry (1 hour)
        
				604800     ; expire (1 week)

				38400      ; minimum (10 hours 40 minutes)

				)

@		IN NS	ns.fabulamaravillosa.int.
ns  	IN A		33.33.5.14

test	IN A		33.33.5.34

www 	IN CNAME	ns

texto	IN TXT		mensaje



# **Parte 3: _Comprobación DIG_**

Si tenemos todo completamente configurado, entraremos en el cliente e instalaremos DIG con _dnsutils_, si lo tenemos correctamente al hacer el comando ***dig 33.33.5.14 test.fabulamaravillosa.int*** debeía dar respuesta.


# **Parte 4.1: _Añadiendo virtual host_**

En el archivo httpd.conf buscamos la línea en la que pone ***listen 80*** y pegamos las siguientes líneas:

**<VirtualHost *:80>**

    DocumentRoot /usr/local/apache2/htdocs/www1

    ServerName www.fabulamaravillosa.int
</VirtualHost>

**<VirtualHost *:80>**

    DocumentRoot /usr/local/apache2/htdocs/www2

    ServerName www.fabulaoscura.int
</VirtualHost>


# **Parte 4.2: _Colocar HTMLs_**

Depende de la ruta que hayas metido en el archivo httpd.conf de la anterior parte, que tiene que ser la misma ruta del apache, tienes que crear ciertas carpetas para poner los ***index.html*** dentro, con la finalidad de comprobar su funcionamiento.
 
En este caso, las carpetas _www1_ y _www2_ son las carpetas donde se van a meter los archivos de HTML.

# **Parte 5: _Comprobación_**

Para acabar, entraremos en una consola del cliente, haremos ***apk update*** y luego instalaremos _lynx_ con el comando ***apk add lynx***.
 
Después usaremos lynx con el siguiente comando:

***lynx www.fabulaoscura.int:80***

Si al pulsar enter te devuelve el contenido html relacionado con esa database, ya estaría hecho.