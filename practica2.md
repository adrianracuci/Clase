# Poĺitica de contraseñas
## Introducción

La seguridad de nuestro sistema puede verse amenazada. Una buena política de contraseñas reforzaría la seguridad.

El objetivo de la practica es entender y configurar las directrices de seguridad, para ello utilizaremos el modulo pam "pw_password".

## Desarrollo

* instalación:

Para la instalación tenemos que poner en el terminal el siguiente comando para que se instale el paquete, cual editaremos para la nueva política de seguridad.

```bash
    sudo apt install libpam-cracklib
```

* desarrollo

Con ese comando ya podemos modificar el archivo
```bash
    sudo nano /etc/pam.d/common-password
```

* configuración

Para la configuración queda a libre elección para poner las nuevas políticas de seguridad. Yo he elegido minimo 6 carracteres, minimo 1 numero, minimo 2 letras en minuscula y minimo 1 letra mayuscula.

Eso en el documento se tiene que configurar de la siguiente manera.

```bash
    retry=3 minlen=6 difok=3 ucretid=-1 lcretid=-2 dcredit=-1
```
Emplicación del texto anterior:

retry: veces que puede intentar cambiar la contraseña.

minlen: minimo de longitud.

difok: carracteres que tiene que cambiar en la nueva contraseña.

ucretid: - = minimo y + = maximo, en este caso minimo una letra mayuscula .

lcretid: minimo dos letras en minuscula.

dcretid: minimo un numero en la contraseña.

## Comprobación

* verificación: la hacemos poniendo el comando:
```bash
    echo 'Hola' | pwscore
```

Saldra que esta mal porque falta un numero y minimo de 6 caracteres.

```bash
    echo 'Holaa1' | pwscore
```
Esta funciona porque cumple con el minimo de: mayuscula, numero y 6 de longitud. Y dara la cantidad de 26 en score.

Y si ponemos carracteres especiales como '%', sale que tenemos mayor puntiación en la seguridad de contraseña.