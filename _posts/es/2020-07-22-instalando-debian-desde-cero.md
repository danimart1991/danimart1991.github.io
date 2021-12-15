---
title: "Instalando Debian 11 desde cero"
header:
  image: /assets/posts/es/instalando-debian-desde-cero/header.png
categories:
  - Tutorial
  - PC   
  - Linux
lang: es
ref: 34
permalink: /es/instalando-debian-desde-cero/
last_modified_at: 2021-12-15
---

Tras probar [*Home Assistant*](https://www.home-assistant.io/) en una [*Raspberry Pi 2*](https://www.raspberrypi.org/) durante varios meses, se hace necesario la compra de un *Mini-PC* para poder ampliar los servicios y dispositivos de mi hogar, es por eso que me he animado a la compra de un [***Medion S22003 MD34639***](https://amzn.to/3j8XQvN).

Los [*Add-ons* de *Home Assistant*](https://www.danielmartingonzalez.com/es/conociendo-home-assistant/#instalando-nuestro-primer-add-on) son bastante útiles, te permiten configurar nuevos servicios de una manera sencilla. Pero muchas veces se quedan cortos en funcionalidad y no todos los servicios están disponibles como *Add-on*. Por lo que me he decidido a realizar la instalación de un sistema operativo basado en *Linux*, [*Docker*](https://www.docker.com/) y todos los servicios como contenedores *Docker*. Esto facilitará la tarea de conservar las configuraciones y datos de cada "aplicación", y la rápida actualización y gestión de cada uno por separado sin que afecte al resto.

En esta lista, el primer punto es instalar [***Debian 11***](https://www.debian.org/) desde cero en el *Mini-PC*.

## Requisitos

- El ordenador/*Mini-PC*/*NUC*... donde vamos a instalar *Debian 11*
- Una memoria *USB*.
- Un ordenador ya configurado para crear una imagen de instalación en la memoria *USB*.

## Descargar y grabar la imagen

Se va a optar por realizar una instalación con un *USB* de arranque que contenga la imagen de *Debian 11*. Lo primero es descargar la imagen y grabarla en la memoria *USB* usando [***Rufus***](https://rufus.ie/).

Desde [la página para la obtención de *Debian 11*](https://www.debian.org/distrib/), seleccionamos la imagen para la arquitectura de nuestra máquina. En mi caso, *PC* de 64 *bits*.

![Descargar imagen Debian 11](/assets/posts/es/instalando-debian-desde-cero/descargar-imagen.jpg)

A continuación, descargamos *Rufus* desde [su página oficial](https://rufus.ie/). También puede usarse cualquier otro software de creación de USBs de arranque.

![Configuración Rufus](/assets/posts/es/instalando-debian-desde-cero/rufus.png)

Inserta la memoria *USB* en el ordenador. Abre *Rufus*, carga la imagen recién descargada en el programa, y selecciona la memoria *USB*. El resto de opciones, se pueden dejar por defecto.

Haz clic en **Empezar**. Cuando termine, cierra la ventana y desconecta la memoria *USB*.

## Instalación Debian 11

**Insertamos la memoria *USB* en el *Mini-PC*** donde vayamos a instalar *Debian 11*, y lo encendemos.

Aparecerá una pantalla con un menú de instalación. Haz clic en ***Install***.

![Menú de instalación](/assets/posts/es/instalando-debian-desde-cero/menu-instalacion.jpg)

### Configuración de idioma

Selecciona el idioma, ubicación y mapa de teclado.

![Selección idioma](/assets/posts/es/instalando-debian-desde-cero/seleccion-idioma.png)

### Configuración de red

A continuación, se cargará la configuración de red para poder descargar los archivos necesarios durante la instalación. En mi caso, el ordenador dispone de conexión *Wi-Fi* y cable de red. El primero de ellos usa unos drivers propietarios, por lo que durante la instalación se hace difícil su configuración, opto por conectar el cable de red y configurar el dispositivo después.

![Error Hardware red](/assets/posts/es/instalando-debian-desde-cero/error-hardware-red.png)

Inserta un nombre para la maquina y un nombre de dominio. Este último suele ser igual para todos los dispositivos de la casa. En mi caso lo dejo vacío al no usar esta opción en mi red.

![Nombre de maquina](/assets/posts/es/instalando-debian-desde-cero/nombre-maquina.png)

### Cuentas de usuario

En la configuración de cuentas de usuario, define una contraseña para el usuario `root` (administrador), y para la cuenta de usuario que se va a usar habitualmente con menores privilegios: nombre completo, nombre de usuario y contraseña.

![Contraseña usuario root](/assets/posts/es/instalando-debian-desde-cero/contraseña-root.png)

![Nombre completo usuario habitual](/assets/posts/es/instalando-debian-desde-cero/usuario-nombre-completo.png)

### Configuración de particiones de disco

Como se va a usar el ordenador exclusivamente como servidor y casi todos los servicios van a estar encapsulados en contenedores *Docker*, se va a configurar el disco duro para usar una única partición de disco, seleccionando el disco duro interno (especial cuidado en no seleccionar la memoria *USB*), e indicar que se van a guardar todos los ficheros en una única partición.

![Partición de disco guiada](/assets/posts/es/instalando-debian-desde-cero/particion-de-disco-guiada.png)

![Selección disco duro](/assets/posts/es/instalando-debian-desde-cero/seleccion-de-disco-duro.png)

![Partición de ficheros única](/assets/posts/es/instalando-debian-desde-cero/particion-unica.png)

Por el momento no se va a configurar *RAID*, *LVM*... Guardamos.

### Configuración de repositorios

Selecciona el país de réplica de *Debian* para el gestor de paquetes, y una dirección *Web* al gusto según nuestro país. Si no conocemos la dirección *Web* de réplica más cercano, podemos elegir `deb.debian.org`.

![Partición de ficheros única](/assets/posts/es/instalando-debian-desde-cero/seleccion-repositorio.png)

Si dispones de *Proxy* para acceder a la red, se puede configurar en el siguiente paso, lo normal es no disponer de ninguno, así que se puede dejar en blanco.

![Configuración de Proxy](/assets/posts/es/instalando-debian-desde-cero/configurar-proxy.png)

### Selección de Software

Ya casi hemos acabado. En la siguiente ventana, el instalador nos preguntará si queremos instalar automáticamente actualizaciones de seguridad en nuestro servidor. Aunque el sistema requiere un mantenimiento respecto a diversas actualizaciones, por defecto activo la opción de instalación automática para corregir vulnerabilidades graves sin tener que estar pendiente.

Por último, en la ventana de selección de *software*, dado que por lo menos en mi caso no quiero entorno de escritorio. Solo selecciono ***SSH Server*** y **Utilidades del sistema**.

![Selección de Software](/assets/posts/es/instalando-debian-desde-cero/seleccion-software.png)

### Instalación completada

Espera unos minutos hasta que se complete la instalación. Quita la memoria *USB* del ordenador y haz clic en continuar para reiniciar el equipo.

![Instalación completa](/assets/posts/es/instalando-debian-desde-cero/instalacion-completada.png)

Al elegir la instalación sin escritorio, al cargar el sistema se mostrará una ventana de consola pidiendo un usuario y su contraseña, podemos usar `root` o el usuario que hemos creado en el paso de configuración de cuentas de usuario.

```bash
Debian GNU/Linux 11 n-primaryserver tty1

n-primaryserver login: _
```

## Bonus: Instalar sudo

`sudo` es un comando que usado antes de otro comando hace que ese comando se ejecute con los privilegios de otro usuario (normalmente `root`). Es muy utilizado para poder usar comandos que necesitan privilegios sin necesidad de cambiar de usuario.

Por defecto `sudo` no está instalado en *Debian*. Para instalarlo, inicia sesión como usuario `root` o activa el modo super usuario con:

```bash
$ su -
```

Instala `sudo` ejecutando:

```bash
$ apt-get install sudo -y
```

Da permisos para usar `sudo` al usuario actual con:

```bash
$ usermod -aG sudo TUUSUARIO
```

Falta comprobar que la siguiente línea esté en el archivo `sudoers`, modificando el archivo con el comando `visudo`:

```bash
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```

Reinicia el ordenador para que los cambios sean efectivos.

## Bonus 2: Actualizar sources.list

`sources.list` es un archivo que indica los repositorios donde *Debian* va a buscar *firmware*, *software*... Es necesario actualizarlo para disponer de recursos que por defecto no se tiene acceso. Ejecutamos el comando:

```bash
$ sudo nano /etc/apt/sources.list
```

Esto abrirá un editor de texto. La idea es añadir `contrib` y `non-free` para acceder a la colección de paquetes mayor. En mi caso, `sources.list` queda de la siguiente forma:

```bash
# deb cdrom:[Debian GNU/Linux 11.1.0 _Bullseye_ - Official amd64 NETINST 20211009-10:07]/ bullseye main

deb http://deb.debian.org/debian/ bullseye main contrib non-free
deb-src http://deb.debian.org/debian/ bullseye main contrib non-free

deb http://security.debian.org/debian-security bullseye/updates main contrib non-free
deb-src http://security.debian.org/debian-security bullseye/updates main contrib non-free

# bullseye-updates, previously known as 'volatile'
deb http://deb.debian.org/debian/ bullseye-updates main
deb-src http://deb.debian.org/debian/ bullseye-updates main
```

A continuación, guarda presionando **F3** y sal del editor con **F2**.

Por último, ejecuta los comandos:

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrade
```

## Conclusión

El ordenador queda configurado de manera básica con ***Debian 11***, con el bonus de tener disponible el muy usado comando `sudo` y el acceso a un mayor catálogo de paquetes. Los siguientes pasos son instalar *Docker* y los contenedores que queramos como servicios para que nuestro ordenador se convierta en un potente servidor de casa.
