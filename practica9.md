# Trabajo Final SGD

## Página WEB

###  Es accesible desde la red externa

### Sirve la página index.html(básica) por defecto

- Paso 1

Para tener la página base tenemos que instalar el servicio apache con el siguiente comando:

```bash
  sudo apt get install apache2
```

- Paso 2

Una vez instalado el servicio apache tenemos que ir a cambiar el archivo index.html para mostrar el nuestro.

```bash
  sudo nano /var/www/html/index.html
```

- Paso 3

En el archivo tenemos que modificar con los detalles que queremos que salga en la página principal.

```bash
  <html>
  <head>
    <title>Página principal</title>
    <meta cahrset="utf-8">
  </head>
  <body>
    <h1>Ejercicio Final Seguridad</h1>
    <p>Aquí ponemos algun texto de ejemplo</p>
  </body>
  </html>
```

- Paso 4

Ahora para poder visualizar el archivo tenemos que ir a un buscador y poner la ip del servidor para poder visualizar el archivo index

```bash
  172.16.81.10
```

### Sirve la documentación elaborada con un procesador de textos en formato PDF

Para poder llevar a cabo este proceso tenemos que hacer los siguientes pasos: 

- Paso 1

Tener la carpeta con los archivos pdf's localizados

```bash
  sudo cd /var/www/html/pdf
```

- Paso 2

Tener archivos con la extensión pdf en la carpeta anteriormente mencionada.

```bash
  ls /var/www/html/pdf
  archivo.pdf
```

- Paso 3

En el archivo de configuración /etc/apache2/apache2.conf tenemos que editar la siguiente línea para poder dar acceso de apache a ese tipo de documentos con esa extensión:

```bash
  sudo nano /etc/apache2/apache2.conf
  --
  AddType application/pdf .pdf 
```

- Paso 4

Reiniciamos el servicio para que se pueda recargar la configuración y que se apliquen.

```bash
  sudo systemctl restart apache2
```

- Paso 5

Ahora para hacer la comprobación tenemos que ir a un cliente y en el navegador ponemos:

```bash
  172.16.81.10/pdf/archivo.pdf
```

Si ponemos esa url, se nos descargara automaticamente un archivo pdf.

### Sirve la documentación elaborada en formato MarkDown como HTML 

- Paso 1

Lo primero qué tenemos qué hacer es la instalación del comando markdown, para ello tenemos que poner el siguiente comando: 

```bash
  sudo apt install markdown
```

- Paso 2

Una vez tenemos el comando tenemos que proceder a crear un archivo .md en el directorio principal qué está definido en el archivo de configuración del servicio apache. 

```bash
  sudo cd /var/www/html/
  sudo nano archivo.md
```

- Paso 3
  
Dentro de archivo.md ponemos lo siguiente como ejemplo: 

```bash
  # Prueba1 
  ##  Prueba 2 
```

- Paso 4

Guardamos el archivo en .md y lo que tenemos que hacer a continuación es cambiar la extensión con el comando instalado anteriormente. 

```bash
  markdown archivo.md > mi_archivo.html
```

- Paso 5
  
Ahora lo que tenemos que hacer para comprobar es poner la url en el cliente para poder ver el archivo

```bash
  172.16.81.10/mi_archivo.html
```
