---
title: "Android TV en Home Assistant"
header:
  image: /assets/posts/es/android-tv-en-home-assistant/header.png
categories:
  - Tutorial
  - Android
  - Home Assistant
lang: es
ref: 41
permalink: /es/android-tv-en-home-assistant/
---

Android TV se incluye en muchas televisiones inteligentes a día de hoy; también como sistema operativo de muchos reproductores multimedia como mejor opción para hacer inteligente una televisión o para revivir una Smart TV con varios años.

Aprovechando varias ofertas, [he comprado un par de Xiaomi Mijia Mi Box S](https://amzn.to/336hXEx) para poder reproducir comodamente mi servidor multimedia y tener disponibles las últimas aplicaciones de Streaming como YouTube, Netflix o HBO. De paso, lo he incluido en [nuestro servidor Home Assistant](https://www.danielmartingonzalez.com/es/conociendo-home-assistant/) para controlar cuando está encendido o apagado y poder controlar las funciones básicas desde mi móvil, ordenador, o incluso asistente de voz.

[Imagen HA con Xiaomi Mi Box]()

## Preparando Android TV

El primer paso es preparar Android TV para aceptar comandos y la conexión con Home Assistant. Esto se realiza usando [Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb).

- Es recomendable que la IP de Android TV sea fija, esto puede conseguirse bloqueando la IP desde el Router o desde el propio Android TV desde el menú Configuración -> Red. Apunta la IP, la vamos a usar luego.

- Desde el menú de configuración de Android TV -> Información, haz click 10 veces en "@@@@@@@@", un mensaje indicará que el menú de desarrollador ha sido activado.

- Por último, accede desde el menú Configuración a Herramientas de Desarrollador y activa la opción ADB Debugging.

> En el caso de Xiaomi Mi Box S, además, es necesario cambiar el valor de USB @@@@ a otro que no sea Cargar. En mi caso, al tener un adaptador USB a Ethernet, activé esta opción, y de paso, arreglé un problema con avisos de conexión al encender el reproductor multimedia.

## 
...




Al [instalar un sistema operativo sin escritorio](https://www.danielmartingonzalez.com/es/instalando-debian-10-desde-cero/) una de las cosas que más se echan de menos es **tener un explorador de archivos** con el que poder realizar tareas básicas. [***File Browser***](https://filebrowser.org/) suple esa necesidad permitiendo el **acceso a los archivos del servidor mediante el navegador web**.

Por si fuera poco, además incluye un **visor y editor de archivos**, y una **terminal para ejecutar comandos**.

![File Browser](/assets/posts/es/explorador-de-archivos-online-para-tu-servidor/file-browser.png)

## Instalación y Uso

Su instalación, como viene siendo costumbre, se realizará [instanciando un contenedor *Docker*](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian/) ejecutando el siguiente comando:

```bash
$ docker run -d --name=Filebrowser --hostname=filebrowser --restart=always -p 8086:80 -v /:/srv -e TZ="Europe/Madrid" filebrowser/filebrowser:latest
```

- `docker run -d`: Crea un contenedor con la siguiente configuración si no existe y lo arranca en segundo plano.
- `--name=Filebrowser`: El nombre del contenedor. Es importante pues podemos tener varios contenedores basados en la misma imagen.
- `--hostname=filebrowser`: El nombre de la máquina del contenedor. Opcional.
- `--restart=always`: Reinicia el contenedor si se para. Si se para manualmente, solo es reiniciado cuando el contenedor se reinicie manualmente o el servicio *Docker* sea reiniciado.
- `-p 8086:80`: *File Browser* expone el puerto `80` para el acceso del Explorador de Archivos. Como el puerto `80` suele ser usado para cualquier servicio *Web*; indicamos que el puerto `80` del contenedor sea expuesto por ejemplo en el puerto `8086`. De esta manera hacemos que otros servicios que deseen usar el puerto `80` puedan usarlo.
- `-v /:/srv`: Vincula una carpeta del servidor (izquierda), con una carpeta del contenedor (derecha). *Filebrowser* usa por defecto la carpeta `/srv` interna de raiz para mostar en el explorador de archivos. Al indicar en la carpeta del servidor la raiz `/`, indicamos que queremos mostrar, todos los archivos del servidor.
- `-e TZ='Europe/Madrid'`: La zona horaria de la máquina del contenedor. Opcional. Es muy útil para que los logs y notificaciones sean con la hora local.
- `filebrowser/filebrowser:latest`: Indica la imagen usada para montar el contenedor. Si no se indica versión cogerá la última versión estable.

A continuación, abre un navegador *Web* e introduce la dirección: `http://IP_DEL_SERVIDOR:8086`. En la siguiente ventana, el usuario y contraseña son `admin`.

![File Browser - Inicio de Sesión](/assets/posts/es/explorador-de-archivos-online-para-tu-servidor/file-browser-login.png)

A partir de aquí, veremos las carpetas y archivos del servidor y podremos realizar las típicas acciones que haríamos en cualquier Explorador de Archivos.

## Opciones

Desde el **menú de opciones** podremos cambiar el idioma o la contraseña del usuario, pero también otras configuraciones como permitir la creación de otros usuarios o activar el modo oscuro.

![File Browser - Configuración](/assets/posts/es/explorador-de-archivos-online-para-tu-servidor/file-browser-settings.png)

## Conclusión

Ahora podrás acceder a los archivos del servidor usando un **Explorador de Archivos**. Una forma cómoda y visual de trabajar.
