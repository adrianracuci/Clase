# LDAP & RADIUS

## Introducción

En este ejercicio, configuraremos un sistema de autenticación con servidores LDAP y RADIUS, junto con la configuración de un router. La combinación de estos elementos permitirá a los clientes autenticarse de forma segura en la red, asegurando un acceso confiable a los recursos.

## Asignación de direcciones IP's

Router:            192.168.82.112

Servidor Radius:   192.168.82.113

Servidor LDAP:     192.168.82.114

## Configuración Servidor LDAP

Lo primero que tenemos que hacer es installar ldap en nuestro servidor, para ello tenemos que ejecutar el siguiente comando:

```bash
  sudo apt get install slapd
```

Una vez instalado tenemos que crear el usuario con el que queremos conectarnos al servidor Radius.

```bash
  nano usr.ldif
-----------------
dn: uid:paco,ou=people,dc=iesgrao,dc=es
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: paco
sn: ocap
givenName: paco
cn: paco ocap
displayName: paco ocap
uidNumber: 10002
gidNumber: 5000
userPassword: server
gecos: paco ocap
loginShell: /bin/bash
homeDirectory: /home/paco
```

Con la creación de este archivo podemos añadir ese usuario al la unidad organizativa. Lo siguiente que tenemos que hacer es enviar ese archivo para que se cree lo que tenemos dentro del archivo.

```bash
ldapadd -x -D cn=admin,dc=iesgrao,dc=es -W -f usr.ldif
```

## Configuración de servidor FreeRadius

Para instalar el servicio freeradius tenemos que poner el siguiente comando

```bash
sudo apt update && sudo apt upgrade
--
sudo apt install freeradius ldap-utils
```
Ahora tenemos que configurar el módulo de LDAP:

```bash
cp /etc/freeradius/3.0/mods-available/ldap /etc/freeradius/3.0/mods-enabled/
---
nano /etc/freeradius/3.0/mods-enabled/ldap
```

En el archivo tenemos que modificar lo siguiente:

```bash
server = '192.168.82.114'
identity = 'cn=admin,dc=iesgrao,dc=es'
password = server
base_dn = 'dc=iesgrao,dc=es'
```

Ahora hace falta iniciar el servicio:

```bash
sudo service freeradius start
```
## Verificación

Para hacer la verificación tenemos que activar freeradius con el siguiente comando y luego hacer peticiones de loggin, para ver que nos deja conectar:

```bash
freeradius -x
```

Ahora tenemos que hacer el loggin para ver que todo esta conectado:

```bash
radtest paco server 192.168.82.114 0 testing123
``` 
