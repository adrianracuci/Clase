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
  sudo apt get install slapd ldap-utils
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

