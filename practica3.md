# Permisos / Reglas ACL

## Introdución
Para esta practica empezamos con la estructuda de carpetas tal que así:

-ESO1: con 10 archivos con formato txt dentro de la carpeta

-ESO2: con 10 archivos con formato txt dentro de la carpeta

-Teachers: con 10 archivos con formato txt dentro de la carpeta

-Students: con 10 archivos con formato txt dentro de la carpeta

Lo qué deseamos es el control de persimosos para cada carpeta según el usuario y grupo que sea la persona que acceda.

El grupo Teacher tiene lectura y escritura en todas las carpeta, la carpeta de ESO1 el grupo eso1 tiene solo permisos de lectura, en la carpeta ESO2 el grupo eso2 tiene solo permisos de lectura y en la carpeta students el grupo student tiene permisos de lectura.

Crear usuarios:
```bash
$ adduser *nombreUsuario*
```

Crear grupos:
```bash
$ addgroup *nombreUsuario*
```

Agregar usuarios a sus respectivos grupos:
```bash
$ adduser *nombreUsuario* *nombreGrupo*
```

Crear directorios:
```bash
$ mkdir *nombreDirectorio*
```
## Implementación de permisos

Para el directorio *teachers*, asignar permisos de lectura al grupo *teachers* y de lectura y escritura al usuario *t1*:
```bash
$ setfacl -Rdm u:t1:rw,g:teachers:r *teachers*
```

Para el directorio *students*, asignar permisos de lectura y escritura al grupo *teachers* y de lectura al grupo *students*:
```bash
$ setfacl -Rdm g:students:r,g:teachers:rw *students*
```

Para el directorio *eso1*, asignar permisos de lectura y escritura al grupo *teachers* y de lectura al grupo *eso1*:
```bash
$ setfacl -Rdm g:eso1:r,g:teachers:rw *eso1*
```

Para el directorio *eso2*, asignar permisos de lectura y escritura al grupo *teachers* y de lectura al grupo *eso2*:
```bash
$ setfacl -Rdm g:eso2:r,g:teachers:rw *eso2*
```
