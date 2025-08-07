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

``docker run -d --name <nombre-contenedor> --network <nombre de red> -p <puerto-a-exponer>:<puerto-contenedor> <nombre de imagen>``

Para el caso de los contenedores de bases de datos, por ejemplo MYSQL, se debe establecer las variables de entorno que serán usadas por el backend para acceder a ella.

``docker run -d --name <nombre-contenedor> --network <nombre de red> -p <puerto-a-exponer>:<puerto-contenedor> -e MYSQL_ROOT_PASSWORD=<password> -e MYSQL_DATABASE=<nombre-base-de-datos> mysql``

Para inspeccionar la red y verificar que los contenedores han sido añadidos de esta, se ejecuta el comando. 

``sudo docker network inspect <nombre-de-red>``

# 4. COMANDOS PARA DEBUGGEAR CONTENEDORES:

Aveces los contenedores pueden caerse debido a un problema en su ejecución.

``sudo docker <nombre-contenedor> logs ``

Permite ver los ultimos registros del contenedor para encontrar fallas.





