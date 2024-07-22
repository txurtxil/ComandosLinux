# ComandosLinux

## Sistema:

Buscar ficheros masyotes de 50MB:

      find /var/lib -type f -size +50000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

Buscar un texto literal dentro de llos ficheros:

      grep -lir texto /

Hacer test de velociad en una unidad (en este caso la MicroSD):

       hdparm -Tt /dev/mmcblk0


## Docker:

  Para Ver procesos de los docker en ejecucion:
  
      sudo docker ps

  Para ver estadisticas de los docker en tiempo real:
  
      sudo docker ps

  Para ver las imagenes docker en el sistema
  
      sudo docker image ls

 ### Borrado de un docker:
  
       sudo docker ps ---> ver qué «Container ID» tiene el contenedor que queremos borrar
       
       sudo docker stop «Container ID»
      
       sudo docker rm   «Container ID»
       
       sudo rm -R carpetadelDocker
  
 ### Copia exacta de un docker     
  
        cd DockerAcopiar
        
Esto parará este contenedor en concreto y mantendrá el resto de contenedores funcionando con normalidad.        

        sudo docker-compose down 
        
        cd ..
Podemos levantar el docker original «sudo docker-compose -p "DockerAcopiar" up -d» y modificar el docker-compose.yl del copiado        
 
        cp -r DockeAcopiar DockeAcopiar2   

  Copias de seguridad de un Docker.
  
Simple lista de los id’s de todos los contenedores que están ejecutándose en nuestra máquina.

        sudo docker ps -q      

Comando que coje cada ID y detiene los contenedor que correspondan con cada línea.
 
        sudo docker stop $(sudo docker ps -q)        
        
Copiamos todos los dockers a una ubicación diferente.

        sudo rsync -av /home/<tu_usuario>/docker /media/usb  

Saber los id’s de los contenedores que están detenidos
      
        sudo docker ps -a -q                                 

Podemos encender los contenedores con el siguiente comando.

        sudo docker start $(sudo docker ps -a -q)           
        
Y con esto quedarían todos los dockers copiados. Podríamos llevarlos a una nueva ubicación (a otro servidor) y levantarlos de nuevo.
        

