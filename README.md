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
Elimina todos los volumenes que no están siendo utilizados
     docker volume prune

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

### Inteligencia artificial


Instalar ollama Comandos utilizados :

      $ git clone https://github.com/jmlcas/ollama
      
      $ cd ollama
      
      $ docker compose up -d

      IA diminuta, mas liviana y rapida:
      $ docker-compose exec ollama ollama pull tinyllama 
      
Comandos utilizados para usarmoelos de la IA deepseek, la r1 mas liviana y la v2 mas potente (pero debemos tener Ollama ya instalado):

      $ docker exec ollama ollama pull deepseek-r1 
      
      $ docker exec ollama ollama pull deepseek-v2

Para eliminar un modelo, por ejemplo deepseek v2:

      $ docker exec ollama ollama rm deepseek-v2


### Compartir con Samba

Instalar seridor samba:

sudo apt update && sudo apt install samba samba-common -y

Editar el fichero /etc/smb.conf (cambiar usaurio por el que usamos en nuesta pi):

      sudo nano /etc/samba/smb.conf

            [512GB]
            path = /mnt/512GB
            browseable = yes
            writable = yes
            read only = no
            guest ok = no
            valid users = usuario
            force user = usuario
            create mask = 0660
            directory mask = 0770
      
Creamos el usuario para samba:

         sudo adduser usuario (cambiar usuario por el que usamos en nuesta pi)
         
Hay que dar clave al usuario a usar en samba:
         sudo smbpasswd -e usuario (cambiar usuario por el que usamos en nuesta pi)

Podemos cambiar propietario de la carpeta a compartir para el ususuario que usamos en la pi:
      
      sudo chown -R usuario:usuario /mnt/512G
      
Reiniciamos servicios:

        sudo service smbd restart
        sudo systemctl restart nmbd

Podemos verificar el estado de Samba:
         sudo smbstatus -S

Montar unidad remota smb con permismo de escritura para un usuario no root (cambiar usuario por el que usamos en nuesta pi)

         sudo mount -t cifs //IP_SERVIDOR/Carpeta_compartida /media/usuario/256GB/duplicati/backups -o username=usuario,password=1234,iocharset=utf8,rw,uid=$(id -u usuario),gid=$(id -g usuario) 

### Instalar duplicati:

Crear la carpeta duplicati y dentro el fichero docker-compose.yml

                  services:
                  
                    duplicati:
                      image: lscr.io/linuxserver/duplicati:latest
                      container_name: duplicati
                      environment:
                        - PUID=1000
                        - PGID=1000
                        - TZ=Europe/Madrid
                        - SETTINGS_ENCRYPTION_KEY=mysecret
                        - DUPLICATI__WEBSERVICE_PASSWORD=p4ssw0rd
                      volumes:
                        #lee las carpetas licales (izq) y mapea con la imagen (decha)
                        - ./config:/config
                        #Si la carpeta backups es remota (con samba) es necesario parar(docker stop duplicati) montar smb y docker start duplicati
                        - ./backups:/backups
                        - /media/usuario/256GB:/source
                      ports:
                        - "8200:8200"
                      restart: unless-stopped

Lanzamos la instalacion:
                  docker-compose up -d
                  Url: http://localhost:8200 (password es p4ssw0rd)
        
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


NOTAS problemas wordpress:

  Si tenemos problemas con la importacion del fichero XML de blogger hay que crear el fichero .htaccess e incluir:

              php_value upload_max_filesize 12M
              php_value post_max_size 13M
              php_value memory_limit 15M
