# ComandosLinux

## Sistema:

Buscar ficheros masyotes de 50MB:

      find /var/lib -type f -size +50000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

Buscar un texto literal dentro de llos ficheros:

      grep -lir texto /

Hacer test de velociad en una unidad (en este caso la MicroSD):

       hdparm -Tt /dev/mmcblk0

Borramos todos los logs que tienen mas de 5 dias a la 1:00 (crontab -e)

      00 01  * * *  su -l root -c '/usr/bin/find /var/log/httpd/*.gz -mtime +5 -type f -exec rm -f  \{\} \;'


## Compartir por NFS:

sudo apt install nfs-kernel-server

Fichero exports

      sudo nano /etc/exports 

Añadimos la linea (nota: asterisco * puede ser sustituido por una IP del cliente si no queremos para todos):
  
      /carpeta/a/compartir  *(rw,all_squash,insecure,async,no_subtree_check,anonuid=1000,anongid=1000)

Reiniciar el servicio (Raspberry):

      sudo systemctl restart nfs-kernel-server

Reiniciar el servicio (Manjario Linux)

      sudo systemctl enable --now nfs-server.service  

Para llegar hasta el equipo que tiene la carpeta compartida:
  
      sudo mount -t nfs EQUIPO_COMARTE:/ /mnt

## Cambiar etiquetas de particiones ext
      
      e2label /dev/sdd1 nueva_etiqueta

## Docker:

  Para Ver procesos de los docker en ejecucion:
  
      sudo docker ps
      
Mostrará información tanto de los dockers que tenemos activos como los que tenemos en el sistema 
pero no se están ejecutando actualmente:

      sudo docker ps --all   

  Para ver estadisticas de los docker en tiempo real:
  
      sudo docker stats

  Para ver las imagenes docker en el sistema
  
      sudo docker image ls

 Iniciar y reiniciar todos los servicios definidos en docker-compose.yml
 
         docker-compose up 
      
 Parar unicamente servicios
 
      docker-compose stop

Apaga y borra containers, networks..

      docker-compose down 

Apagar y quitar volúmenes 

      docker-compose down --volumes 

Apagar y quitar images

      docker-compose down --rmi <all|local> 
      
Elimina todas las imágenes que no están siendo utilizadas en ningún contenedor en ejecución.
     
     sudo docker image prune

Para iniciar sesion en el contendor de un docker:

      sudo docker exec -it <nombre_del_contenedor> /bin/bash

Para que los docker se lanzen en el reboot de maquina:

      docker compose up -d

 ### Borrado de un docker:

Ver qué «Container ID» tiene el contenedor que queremos borrar:

       sudo docker ps 

Paramos el docker:       

       sudo docker stop «Container ID»

Borramos el docker:

       sudo docker rm   «Container ID»

Borramos finalmente la carperta del docker:

       sudo rm -R carpetadelDocker
  
 ### Copia exacta de un docker     

Entramos en el docker a copiar:
  
        cd DockerAcopiar
        
Este comando parará este contenedor en concreto y mantendrá el resto de contenedores funcionando con normalidad:

        sudo docker-compose down 
        
Vamos a un nivel inferior:

        cd ..

Procedemos a copiar el docker:
        
        cp -r DockeAcopiar DockeAcopiar2

Podemos levantar el docker original «sudo docker-compose -p "DockerAcopiar" up -d» y modificar el docker-compose.yml del copiado para trabajar en el.
    

### Copias de seguridad de un Docker.
  
Simple lista de los id’s de todos los contenedores que están ejecutándose en nuestra máquina.

        sudo docker ps -q      

Comando que coje cada ID y detiene los contenedor que correspondan con cada línea.
 
        sudo docker stop $(sudo docker ps -q)        
        
Copiamos todos los dockers a una ubicación diferente.

        sudo rsync -av /home/<tu_usuario>/docker /media/usb  

Excluimos el contenido de una carpeta de la copìa:

        rsync -a --exclude 'transmission/downloads/complete/*' /media/txurtxil/256GB/ /mnt/docker/

Si queremos un tar.gz de la carpeta docker conservando permisos, comprimiendo y excluyendo carpetas:

         sudo tar -czf docker.tar.gz --exclude='/media/txurtxil/256GB/transmission/downloads/complete/*' /media/txurtxil/256GB/

Saber los id’s de los contenedores que están detenidos
      
        sudo docker ps -a -q                                 

Podemos encender los contenedores con el siguiente comando.

        sudo docker start $(sudo docker ps -a -q)           
        
Y con esto quedarían todos los dockers copiados. Podríamos llevarlos a una nueva ubicación (a otro servidor) y levantarlos de nuevo.
        
### Instalar docker wordpress:

Crear la carpeta wordpress y dentro el fichero docker-compose.yml

                  version: '3.1'
                  
                  services:
                  
                    wordpress:
                      image: wordpress
                      restart: always
                      ports:
                        - 8080:80
                      environment:
                        WORDPRESS_DB_HOST: db
                        WORDPRESS_DB_USER: exampleuser
                        WORDPRESS_DB_PASSWORD: examplepass
                        WORDPRESS_DB_NAME: exampledb
                      volumes:
                        - wordpress:/var/www/html
                  
                    db:
                      image: mysql:8.0
                      restart: always
                      environment:
                        MYSQL_DATABASE: exampledb
                        MYSQL_USER: exampleuser
                        MYSQL_PASSWORD: examplepass
                        MYSQL_RANDOM_ROOT_PASSWORD: '1'
                      volumes:
                        - db:/var/lib/mysql
                  
                  volumes:
                    wordpress:
                    db:

Lanzamos la instalacion y elecucion silenciosa:

           sudo docker-compose -p "wordpress" up -d

Yas tenemos lista la insdtalacion de wordpress en la url:

             http://direcc-ip:8080

