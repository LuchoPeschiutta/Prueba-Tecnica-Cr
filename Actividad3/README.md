
# Introducción

Para la realización del pipeline de la actividad 3, seleccioné como herramienta GitHub Actions y para que los workflows no afecten a todo el proyecto, sinó solo a este punto, decidí hacerlo en un repositorio aparte e incluirlo en este como un submodulo.

# Despliegue

Por defecto Git no descarga los submodulos en sus proyectos, por lo cual luego de clonar el código desde github (https://github.com/LuchoPeschiutta/Prueba-Tecnica-Cr.git), será necesario ejecutar en su directorio raiz el siguiente comando:
``` bash
    $ git submodule update --init 
```
De esta forma, ya tendremos los binarios del repositprio del punto 3 (https://github.com/LuchoPeschiutta/Cr_act3.git) en el directorio **./Actividad3/Cr_act3**, aunque otra opción es clonar directamente dicho repositorio.

En su interior encontraremos el **index.html** (cuya modificación impactará en la imagen de nginx), un **Dockerfile** que nos permite buildear la imagen con los cambios realizados y un **docker-compose.yml** para lanzar el servicio localmente.

# Pipeline

El pipeline propiamente dicho está descripto mediante código en el archivo **./.github/workflows/pipeline.yml**. En el, nos encontramos con dos jobs, detallados mas adelante, que se ejecutaran ante un **push** o **pull request** a la rama ***main***, en los cuales se haya modificado el archivo **index.html**.

## build_upload
Pasos:

1. Checkout del repo en la maquina virtual provista por GitHub (ubuntu 20.04).
2. Test de codigo estático en busca de errores sintacticos para evitar perder grandes cantidades de tiempo en un building que fallará. También se pueden y deberían configurar para encontrar otros errores (memory leacks, seguridad, etc).  
No lo he implementado porque en este caso no habrá errores que impidan que se levante el archivo, pero es buena práctica en la mayoría de los proyectos, e incluso aquí se podría chequear la existencia de ciertas etiquetas html que no deben faltar.
3. Building de la imagen con los cambios realizados.
4. Test de Aceptación que compruebe al menos algunas de las funciones mas básicas de la app, con el objetivo de evitar desplegar una versión que no funcione. 
En este caso solo verifico de manera local que al crear un container con la imagen construida, esta levante el servicio en el puerto 80. Para ello, hago uso del comando **curl**.
5. Login a DockerHub para poder subir la imagen.
6. Upload de la imagen a DockerHub (se pueden usar otros servicios de registro como ECR de AWS) con el fin que este disponible para luego hacerle un pull desde la plataforma en la cual se despliega.

## deploy 
Solo se llevará a cabo si la tarea anterior se completó de manera exitosa, lo que asegura que exista una imagen que poder desplegar.

Pasos:

1. Conexión con la plataforma para indicar la descarga y despliegue de la nueva versión de la imagen.  
No esta implementada, pero consiste en el login en alguna plataforma mediante credenciales que se pueden alojar en los secrets del repositorio, para luego indicar que se debe actualizar el servicio a la nueva imagen.