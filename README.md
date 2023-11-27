**Parte 1: _Docker compose_**

Para el docker compose, usaremos de plantilla uno que ya tengamos hecho, este docker compose debe tener un servidor apache, un servidor DNS y un cliente para hacer las pruebas.


El docker compose debe quedar algo así: 

services:

  servidor_apache:

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

  servidor_dns:

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

  cliente:

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


networks:

  red_33:

    external: true
