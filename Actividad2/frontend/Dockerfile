
#Se selecciona alpine porque es ligero y permite una facil gestion de paquetes.
FROM node:20-alpine

#Directorio a utilizar dentro de la imagen
WORKDIR /app

#Copiamos todos los archivos al interior de la imagen
COPY . .

#Instalamos los paquetes necesarios para la aplicacion (packaje.json)
RUN npm install 

#Indicamos la exposicion del puerto 3000 para que sea posible comunicarse con el servicio
EXPOSE 3000