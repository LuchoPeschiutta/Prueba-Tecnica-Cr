
# Instrucciones para la compilación y el despligue de la aplicación
---
---
## Despliegue desde PC local

- Clonamos el repo desde github (https://github.com/LuchoPeschiutta/Prueba-Tecnica-Cr.git).
- Nos ubicamos sobre el directorio Actividad2 desde una terminal linux.
- Ingresamos el comando: 
``` bash
    $ docker-compose up
```
si queremos ver el log de los servicios, o bien:
``` bash
    $ docker-compose up -d
```
si queremos que se ejecute en background.

---

### Extras

Para detener el servicio y revertir las configuraciones se debe utilizar el comando:
``` bash
    $ docker-compose down
```
De esta manera borramos las redes y contenedores utilizados.

Tambien podríamos querer borrar de nuestro sistema las imágenes creadas para los servicios y las imágenes utilizadas para el building. Para este fin podemos listar las imágenes en nuestro sistema mediante:
``` bash
    $ docker images
```
y luego eliminar las que deseemos mediante:
``` bash
    $ docker rmi "imagen"
```

---
---

## Despliegue en la nube

Para el despliegue en la nube se utilizará el servicio ECS de AWS. Los pasos serán los siguientes:
- Crear una cuenta en AWS o ingresar a ella.
- Buildear las imágenes de docker de nuestros servicios mediante los archivos Dockerfile:
    - Para esto nos posicionamos sobre el directorio Actividad2 desde la terminal de linux y ejecutamos:
    ``` bash
        $ docker build -t backend-s ./backend/
        $ docker build -t frontend-s ./frontend/
    ```
- Ahora que tenenemos nuestras imagenes construidas, con los nombres de backend-s y frontend-s, las subimos a un registro de contendores en la nube, para que esten a disposición del servicio ECS. Para este caso usaremos el servicio ECR de AWS:
    - Ir al servicio ECR desde la página de AWS y crear un nuevo repositorio privado.
    - Subimos las imágenes al repositorio mediante los comandos descriptos en la pestaña "View push commands" de la misma página.
- Ir al servicio ECS y crear un cluster de contenedores. Para esto se debe elegir entre utilizar un cluster en EC2 (el cual debemos administrar) o mediante el servicio
Fargate de AWS que es serverless y nos abstrae de esta gestión. Utilizaremos Fargate en este caso.
- Dentro ahora de nuestro cluster previamente creado, crearemos una nueva tarea, la cual estará compuesta por nuestros dos contenedores.
    - Seleccionamos las imágenes previamente subidas a ECR para nuestros contenedores y mapeamos los puertos 8000 para el backend y 3000 para el frontend.
- Finalizada la creación de la tarea, nos vamos de nuevo a la ventana del cluster y en la parte de servicios desplegamos un nuevo servicio.
    - El servicio en cuestión será sencillamente lanzar la tarea que creamos. A partir de este momento ya deberíamos poder acceder a ellos.

---

### Extras

- Hay una configuración muy necesaria a realizar cuando se trabaja en la nube de AWS y tambien puede ser la explicación de porque, en algunos casos, aún no se puede acceder a los servicios habiendo completado todos los pasos anteriores. Estamos hablando de los Security Groups, que actuan como firewalls de los dispositivos de nuestra red y es de suma importancia que esten bien cofigurados para brindar seguridad extra a nuestro sistema. Si queremos que nuestros servicios sean visibles desde la red, debemos crearles un Security Group a cada servicio, que permita el tráfico desde cualquier dirección y puerto hacia nuestros puertos 8000 y 3000 para cada servicio respectivamente. Es recomendable bloquear el resto de tráfico completamente, por cuestiones de seguridad.
- Otras características muy útiles brindadas por AWS, que forman parte de ECS o se complementan bien con el, son:
    - El balanceo de carga, en caso de que quisieramos expandir horizontalemente nuestra aplicación, nos permite repartir equitativamente o mediante políticas bien definidas, las cargas entre las tareas.
    - El auto escalado, configurado a la hora de levantar el servicio, nos permite que se replique la tarea automáticamente en caso de que la demanda del servicio aumente y empieze a saturarse. En conjunto con el balanceador de carga nos permite darle elasticidad a nuestra aplicación, para que aunmente y disminuya la cantidad de recursos utilizados en función de la demanda.