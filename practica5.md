##Introducción

Internet es un canal no seguro; cualquier persona podría obtener nuestros datos. Para evitar que esto suceda, es crucial cifrar ciertas comunicaciones que contienen información confidencial. En este sentido, vamos a generar un certificado autofirmado para un servidor web Apache.

#Instalación
```bash
sudo apt install apache2
sudo apt update
```
Paso 1: Instalamos el conjunto de secuencias de comandos easy-rsa:

```bash
sudo apt install easy-rsa
```

Paso 2: Preparamos un directorio para la infraestructura de clave pública:

```bash
sudo mkdir ~/easy-rsa
```

Paso 3: Creamos enlaces simbólicos que apunten a los archivos del paquete easy-rsa:
```bash
sudo ln -s /usr/share/easy-rsa/* ~/easy-rsa/
``

Paso 4: Restringimos el acceso para que solo el propietario pueda acceder a él:

```bash
sudo chmod 700 /home/adrian/easy-rsa
```

Paso 5: Iniciamos el PKI dentro del directorio easy-rsa:

```bash
cd ~/easy-rsa
./easyrsa init-pki
```

Paso 6: Creamos y editamos el archivo vars con los datos de la organización:
```bash
sudo nano ~/easy-rsa/vars
```

~/easy-rsa/vars:

set_var EASYRSA_REQ_COUNTRY    "España"
set_var EASYRSA_REQ_PROVINCE   "Valencia"
set_var EASYRSA_REQ_CITY       "Valencia"
set_var EASYRSA_REQ_ORG        "ASIR2SGD"
set_var EASYRSA_REQ_EMAIL      "adrrac@alu.edu.gva.es"
set_var EASYRSA_REQ_OU         "Community"
set_var EASYRSA_ALGO           "ec"
set_var EASYRSA_DIGEST         "sha512"

##Generación del Certificado

Paso 1: Creamos el certificado root público y el par de claves privadas para su entidad de certificación:

Copiamos todo el contenido del archivo ~/easy-rsa/pki/ca.crt:

  $ nano /tmp/ca.crt
  ```

## Crear CSR - Solicitud

- Paso 1

Ejecutamos los siguientes comandos para verificar la instalación del servicio openssl en el sistema:
```bash
  $ sudo apt update
  $ sudo apt install openssl
  ```

- Paso 2

Creamos un directorio y generamos una clave privada dentro de él:
```bash
  $ sudo mkdir ~/practice-csr
  $ sudo cd ~/practice-csr
  $ sudo openssl genrsa -out adrianracuci-server.key
  ```

- Paso 3

Generamos la solicitud:
```bash
  $ openssl req -new -key adrianracuci-server.key -out adrianracuci-server.req
  ```

## Firma por CA - Firmar solicitud

- Paso 1

Navegamos al directorio de la Autoridad Certificadora (CA) e importamos la CSR:
 ```bash
  $ cd ~/easy-rsa
  $ ./easyrsa import-req adrianracuci-server.req adrianracuci-server
  ```

- Paso 2

Firmamos la CSR:
 ```bash
  $ ./easyrsa sign-req server adrianracuci-server
  ```

## Configurar Apache

- Paso 1

Editamos el archivo /etc/apache2/sites-available/default-ssl.conf:
```bash
  $   sudo nano /etc/apache2/sites-available/default-ssl.conf
  SSLCertificateFile      /etc/ssl/certs/adrianracuci-server.crt
  SSLCertificateKeyFile   /etc/ssl/private/adrianracuci-server.key
  ```

- Paso 2

Movemos el certificado y la clave privada al directorio mencionado anteriormente:
```bash
  $ cd ~/practice-csr
  $ sudo cp adrianracuci-server.crt /etc/ssl/certs/
  $ sudo cp adrianracuci-server.key /etc/ssl/private/
  ```

- Paso 3

Habilitamos el sitio y el módulo SSL:
 ```bash
  $ sudo a2ensite default-ssl.conf
  $ sudo a2enmod ssl
  ```

- Paso 4

Reiniciamos Apache:
 ```bash
  $ systemctl restart apache2
  ```

- Paso 5

Accedemos al navegador, buscamos nuestro nuevo sitio con HTTPS y deberíamos ver el siguiente mensaje:
![riesgo](img/riesgo.png)

## Importar el certificado

Vamos a la configuración del navegador que utilicemos, buscamos la sección de certificados y hacemos clic en 'Ver certificados'. Navegamos a la pestaña de 'Autoridades' y seleccionamos 'Importar...'. Buscamos el certificado de nuestra Autoridad Certificadora (CA), que se encuentra en ~/easy-rsa/pki/ca.crt. Una vez que vemos nuestra Autoridad Certificadora en la lista, hacemos clic en 'Aceptar'.
![ca](img/ca.png)

## Verificación

Al acceder a nuestro nuevo sitio con HTTPS, deberíamos ver que la conexión es segura:
 ![seguridad](img/seguridad.png)
