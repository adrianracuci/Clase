# RAID 10 y LVM

## Introducción

La redundancia implementa un nivel mas de seguridad cuando se rompe, y esta práctica implementamos un raid 10 que gestionamos con volúmenes lógicos.

## Derarollo

Tenemos agregados los 4 discos para poder hacer el raid 1+0. Tenemos que ir al terminal y hacer los ejecutar los siguientes comandos para poder crear el raid.

```bash
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
```

Con esto creamos la primera parte de la practica lo siguiente que tenemos es crear la otra mitad del raid y luego juntar las dos partes en un raid0

```bash
mdadm --create /dev/md1 --level=1 --raid-devices=2 /dev/sdd /dev/sde
```

Y ya con esto creado podemos ejecutar el ultimo comando y realizar el raid 1+0

```bash
mdadm --create /dev/md2 --level=0 --raid-devices=2 /dev/md0 /dev/md1
```

Con esto tendriamos creado el raid, ahora hace falta que lo montemos.

Para ello nos vamos a root y ponemos el siguiente comando.

```bash
mkdir /datos00

mount /dev/md2 /datos00
df -h
fdisk -f
exit
```

Ahora vamos a hacer una comprobación de que funciona el disco creando un archivo que pese 1.5Gb con el siguiente comando y borrar/añadir información para ver que se crea/modifica correctamente.

```bash
sudo dd if=/dev/urandom of=prueba.txt bs=1500MG count=1
```

Lo siguiente para la comprobación es que tenemos que guardar esa información y luego comprobar si sigue igual al modificar ese archivo. Para ello tenemos que emplear el comando 

```bash
md5sum prueba.txt > checksum.txt

echo "prueba" >> prueba.txt

md5sum -c checksum.txt
```

Con esto hacemos: escribir en el archivo checksum un codigo unico sobre la información del archivo prueba y al añadir información con echo a prueba nos tiene que salir que no coincidide la información de la primera vez con la segunda de la comprobación. Esto pasa porque el archivo prueba.txt se le ha agregado información y el archivo cambía, al cambiar no coincide con la información que hay anteriormente. Con esto vemos que el raid funciona, pero vamos ha hacer otra conmprobación añadiendo a los raids 1 otro disco de "seguridad" por si uno de los dos que tenemos deja de funcionar.
