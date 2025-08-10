# 1. ESTRUCTURA BÁSICA DE UN DOCKERFILE

A continuación se presenta la estructura básica de un Dockerfile.

````
FROM eclipse-temurin:21-jdk-jammy  

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
````

**FROM:** 

Indica el lenguaje que se usará.

**WORDIR:**

Equivalente a hacer ``cd``, se usa para ubicarse dentro de un directorio en un contenedor.

**COPY:**

Indica las carpetas que se copiaran desde un directorio hacia el contenedor.

**EXPOSE:**

Describe el puerto del contenedor que será expuesto.

**ENTRYPOINT:**

Comando que se ejecutan cuando se inicia el contenedor. En el ejemplo se ejecuta ``java -jar app.jar`` para poner el archivo compilado en ejecución.

# 2. CONSTRUCCIÓN DE IMAGENES:

El comando para construir una imagen a partir de un Dockerfile viene dado por:

``docker build -t <NombreImagen> .`` 

Suponiendo que el Dockerfile se encuentra en la carpeta actual.

# 3. COMUNICACIÓN ENTRE CONTENEDORES:

La comunicación entre contenedores se realiza mediante networks.

La creación de una network se realiza de la siguiente manera.

``sudo docker network create --attachable <nombre de red>``

Para unir un contenedor a la red, se ejecuta el comando.

``docker run -d --name <nombre-contenedor> --network <nombre de red> -p <puerto-a-exponer-dentro-de-la-mv>:<puerto-contenedor-donde-corre-servicio> <nombre-de-imagen>``

Para el caso de los contenedores de bases de datos, por ejemplo MYSQL, se debe establecer las variables de entorno que serán usadas por el backend para acceder a ella.

``docker run -d --name <nombre-contenedor> --network <nombre de red> -p <puerto-a-exponer-dentro-de-la-mv>:<puerto-contenedor-donde-corre-servicio> -e MYSQL_ROOT_PASSWORD=<password> -e MYSQL_DATABASE=<nombre-base-de-datos> mysql``

Algunas veces el environment no se encuentra seteado en el backend y debe ser pasado como argumento en el docker run. En este caso, mysql debe ser el nombre del contenedor de base de datos.

``docker run -d --name <nombre-contenedor> --network <nombre-red> -p <puerto-a-exponer-dentro-de-la-mv>:<puerto-contenedor-donde-corre-servicio> --env spring.datasource.url=jdbc:mysql://mysql:3306/db_ecommerce <nombre-de-imagen>``

Para inspeccionar la red y verificar que los contenedores han sido añadidos de esta, se ejecuta el comando. 

``sudo docker network inspect <nombre-de-red>``

# 4. COMANDOS PARA DEBUGGEAR CONTENEDORES:

Aveces los contenedores pueden caerse debido a un problema en su ejecución.

``sudo docker logs <nombre-contenedor> ``

Permite ver los ultimos registros del contenedor para encontrar fallas.


# 5.AUTOFIRMADO DE CERTIFICADOS SSL.

Se genera una llave privada:

``openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out barcino.tel.key``

Se genera la solicitud del certificado.

``openssl req -new -key barcino.tel.key -out barcino.tel.csr``

Se autofirma dicho certificado.

``openssl x509 -req -days 365 -in barcino.tel.csr -signkey barcino.tel.key -out barcino.tel.crt``

Se ubica dichos certificados en el archivo ``/etc/nginx/sites-available/default``

````
listen 443 ssl default_server;
listen [::]:443 ssl default_server;

ssl_certificate /etc/nginx/sites-available/barcino.tel.crt;
ssl_certificate_key /etc/nginx/sites-available/barcino.tel.key;
ssl_protocols TLSv1.2 TLSv1.3;

location / {                                                                                                                                                            
                # First attempt to serve request as file, then                                                                                                                  
                # as directory, then fall back to displaying a 404.                                                                                                             
                try_files $uri $uri/ =404;                                                                                                                                      
                proxy_set_header Host $host;                                                                                                                                    
                proxy_set_header X-Real-IP $remote_addr;                                                                                                                        
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;                                                                                                    
                proxy_set_header X-Forwarded-Proto $scheme;                                                                                                                     
        }   
````

