# Trabajo Final SGD

## LDAP

###  La gestion de LDAP se lleva a cabo mediante GUI

- Paso 1

Instalamos lo necesario para poder acceder al servidor LDAP mediante entorno grafico.

```bash
  sudo apt-get install sldap ldap-utils.
  sudo apt-get install apache2 php libapache2-mod-php
  sudo apt-get isntall ldap-account-manager
```

- Paso 2

Acceder a la página web para poder editar LDAP.

```bash
  http://172.16.82.15/lam
```

- Paso 3

Allí le damos a "Edit server profiles" y entramos con la contraseña que viene por defecto, pero la cambiamos para una mas segura.

En "Server settings", tenemos que cambiar la configuración que sale pre-determinada con la que tenemos nosotros:

    - Server address: 172.16.82.15
    - Tree suffix: dc=aba,dc=ldap

### El servidor RADIUS es capaz de autentificarse con los usuarios del directorio

- Paso 1

Instalamos RADIUS para  poder hacer este paso:

```bash
  sudo apt-get install freeradius
```

- Paso 2

Con la instalación hecha tenemos que configurar el archivo etc/freeradius/3.0/mods-available/ldap para poder hacer la autentificación con LDAP

```bash
  ldap {
    server = "aba.ldap"
    identity = "admin"
    password = "admin"
    ...
  }
```

- Paso 3

Una vez tenemos creado esto, reiniciamos el servicio para que se apliquen las modificaciones

```bash
  sudo systemctl restart freeradius
```

### El servidor PROXY es capaz de autentificarse con los usuarios del directorio

- Paso 1

Cuando tengamos un usarios en ldap tenemos que configurar squid para que permita la autentificación con ldap.
Para ello tenemos que poner esto en el archivo /etc/squid/squid.conf

```bash
  auth_param basic program /usr/lib/squid/basic_ldap_auth -v 3 -b "dc=aba,dc=ldap" -D "cn=admin,dc=aba,dc=ldap" -W /path/to/passwordfile -f "(&(objectClass=posixAccount)(uid=%s))"
  auth_param basic children 5
  auth_param basic realm Autenticación LDAP
  auth_param basic credentialsttl 2 hours
  auth_param basic casesensitive off
  
  acl ldap_users proxy_auth REQUIRED
  http_access allow ldap_users
```

- Paso 2

Reiniciar el servicio:

```bash
  sudo service squid restart
```

Y para la comprobación tenemos que tener activado el proxy en el navegador y acceder a interner, nos saldra un loggin donde pondremos un usuarios que pertenezca a ldap.

### El directorio incluye usuarios/usuario invitado

- Paso 1

Creamos un usuario llamado invitado:

```bash
  sudo nano invitado.ldif

  dn: uid=invitado,ou=usuarios,dc=aba,dc=ldap
  objectClass: top
  objectClass: person
  objectClass: organizationalPerson
  objectClass: inetOrgPerson
  uid: invitado
  cn: Usuario Invitado
  sn: Invitado
  givenName: Usuario
  userPassword: passwd
```

- Paso 2

Añadimos el usuario al directorio y reiniciamos ldap:

```bash
  ldapadd -x -D "cn=admin,dc=aba,dc=ldap" -W -f invitado.ldif
  sudo systemctl restart sldap
```

- Paso 3

Añadimos la regla en el squid para que podamos autenticarnos con invitado, en el archivo de /etc/squid/squid.conf:

```bash
  auth_param basic program /usr/lib/squid3/basic_ldap_auth -v 3 -b "dc=aba,dc=ldap" -D "cn=admin,dc=aba,dc=ldap" -W /path/to/passwordfile -f "(&(objectClass=posixAccount)(!(uid=invitado)))"
```

- Paso 4

Y ahora reiniciamos squid

```bash
  sudo service squid restart
```



## RADIUS

### El punto de acceso está configurado de forma correcta

- Paso 1

Ya tenemos instalado freeradius ahora tenemos que configurarlo para que nos podamos conectar.
Lo que tenemos que hacer es ir al archivo /etc/freeradius/clients.conf y configurar lo siguiente:

```bash
  client tu_ap {
    ipaddr = 172.16.82.50
    secret = cliente
    require_message_authenticator = no
  }
```

- Paso 2

Editamos el archivo /etc/freeradius/modules/ldap

```bash
  ldap {
    server = "aba.ldap"
    identity = "cn=admin,dc=aba,dc=ldap"
    password = "ldap"
    basedn = "dc=aba,dc=ldap"
    filter = "(uid=%{Stripped-User-Name:-%{User-Name}})"
    base_filter = "(objectClass=posixAccount)"
    access_attr = "uid"
    groupname_attribute = "cn"
    groupmembership_filter = "(memberUid=%{Stripped-User-Name:-%{User-Name}})"
  }
```

- Paso 3

Editamos el archivo /etc/freeradius/sites-enabled/default y agregamos la sección authorize para incluir el módulo files para autenticación local

```bash
  authorize {
    files
    ldap
  }
```

- Paso 4

Editamos el archivo /etc/freeradius/users y agregamos usuarios locales en este caso:

```bash
  usuario Cleartext-Password := "usuario"
```

- Paso 5

Reiniciamos freeradius para que se apliquen los cambios

```bash
  sudo service freeradius restart
```

### Los clientes inalámbricos son capaces de autenticarse en el directorio  mediante el protocolo RADIUS

- Paso 1

