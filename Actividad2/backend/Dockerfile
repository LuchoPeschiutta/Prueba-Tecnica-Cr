
#Se seleciono esta version de python porque no trae problemas de compatibilidad con funciones de algunos de los paquetes de los requerimientos.
#Versiones mas recientes son incompatibles con el comando 2to3 implementado en algunas dependencias.
#Tambien se selecciono alpine porque es ligero y permite una facil gestion de paquetes.
FROM python:3.8-alpine

#Directorio a utilizar dentro de la imagen
WORKDIR /app

#Instalamos dependencias necesarias
RUN apk update &&\
    apk add libpq-dev python3-dev &&\
    apk add libjpeg jpeg jpeg-dev &&\
    apk add zlib zlib-dev &&\
    apk add gcc musl-dev &&\
    apk add graphviz-dev

#Copiamos el archivo de requerimientos
COPY ./requirements.txt .

#El codigo siguiente es una alternativa para trabajar con versiones de python mas recientes y poder intalar los paquetes requeridos
#RUN pip3 install "setuptools==58.0.0"

#Instalamos los paquetes del archivo
RUN  pip3 install -r requirements.txt

#Copiamos todos los archivos de la aplicacion 
COPY . .

#Indicamos la exposicion del puerto 8000 para que sea posible comunicarse con el servicio
EXPOSE 8000

