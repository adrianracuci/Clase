# Logs Centralizados

## Introducción
El trabajo del administrador de sistemas puede verse simplifacado centralizando los logs, para ello utilizaremos la herramienta rsyslog
## Desarrollo
La practica consta de dos partes, una en el servidor y otra en el cliente.

*---En el cliente---*:


Tenemos que modificar el archivo /etc/rsyslog.conf:
```bash
    nano /etc/rsyslog.conf
```
y al final del archivo agregar la ip del servidor para que los logger del cliente se envien a la ip del servidor. También añadiendo el puerto al final de la Ip.
```bash
    *.* @IP_SERVIDOR:514
```
Seguido tenemos que guardar ese documento y poner el siguiente comando
```bash
    logger "prueba"
```

*---En el servidor---*:


Tenemos que aberriguar primero la Ip de la maquina para poder decir al cliente.
```bash
    ifconfig 
```
En el archivo /etc/rsyslog.conf tenemos que descomentar la siguiente linea:
```bash
    module(load="imudp")
    input(type="imudp" port="514")
```
Una vez descomentado eso tenemos que aplicar el siguiente comando para poder habilitar el puerto para el cortafuegos para que se vaya a comunicar el servidor con el cliente.
```bash
    sudo ufw allow 514/udp
    sudo ufw allow 514/tcp     
```
```bash
    sudo ufw enable
    sudo ufw reload     
```
El primer comando es para activarlos y el segundo para recargar el cortafuegos.

Y por ultimo para ver el mensaje que se envio por logger desde el cliente tenemos que poner:
```bash
    tail /var/log/syslog
```

Y la ultima linea tendría que ser el logger del cliente.