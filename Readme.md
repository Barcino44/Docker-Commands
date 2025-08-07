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

Comando que se ejecutan cuano se inicia el contenedor. En el ejemplo se ejecuta ``java -jar app.jar`` para poner el archivo compilado en ejecución.

# 2. Construcción de imágenes:

El comando para construir una imagen a partir de un Dockerfile viene dado por:

``docker build -t <NombreImagen> .`` 

Suponiendo que el Dockerfile se encuentra en la carpeta actual.







