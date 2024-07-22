# ComandosLinux

## Sistema:

Buscar ficheros masyotes de 50MB:

      find /var/lib -type f -size +50000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

Buscar ficheros masyotes de 50MB:

      find /var/lib -type f -size +50000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

Hacer test de velociad en una unidad (en este caso la MicroSD):

       hdparm -Tt /dev/mmcblk0
