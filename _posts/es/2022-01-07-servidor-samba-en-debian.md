---
title: "Tus archivos desde Windows, instalando un Servidor Samba en Debian"
header:
  image: /assets/posts/es/servidor-samba-en-debian/header.png
  teaser: /assets/posts/es/servidor-samba-en-debian/teaser.png
categories:
  - Tutorial
  - Debian
tags:
  - Servidor
  - Debian
  - Tutorial
  - Samba
  - SMB
  - Windows
lang: es
ref: 46
permalink: /es/servidor-samba-en-debian/
---

Pese a tener diversas formas de [acceder a los archivos del servidor](https://www.danielmartingonzalez.com/es/explorador-de-archivos-online-para-tu-servidor/), quizás todas ellas se hacen algo tediosas si estamos acostumbrados a la navegación por carpetas de _Windows_. Hace poco he descubierto una manera muy sencilla de disponer [**un servidor de archivos _Samba_**](https://www.samba.org/) en [_Debian_](https://www.debian.org/index.es.html) y derivados para poder acceder desde un cliente _Windows_ como si fuera una carpeta más a los archivos del servidor.

¿Por qué necesitamos un servidor _Samba_ para acceder desde _Windows_ a los archivos en un servidor _Linux_? Bueno. Por defecto, ambos sistemas utilizan protocolos de archivos compartidos distintos. _Samba_ es una implementación libre del protocolo de archivos compartidos de _Microsoft Windows_ (antiguamente llamado _SMB_, renombrado recientemente a _CIFS_) para sistemas de tipo _UNIX_.

## Instalación

> La mayoría de los siguientes comandos necesitan privilegios `root` para su ejecución. Puedes usar `sudo` o iniciar sesión como usuario `root` y ahorrarte escribir el prefijo en cada comando.

Instalamos el paquete _Samba server_:

```shell
$ sudo apt update
$ sudo apt install samba -y
```

Habilitamos el servicio _Samba_ para que inicie con el sistema:

```shell
$ sudo systemctl enable smbd
```

Si usamos el _firewall_ del sistema, necesitamos abrir los siguientes puertos:

```shell
$ sudo ufw allow 139
$ sudo ufw allow 445
```

Reiniciamos el servicio `smbd` y comprobamos que está ejecutándose:

```shell
$ sudo systemctl restart smbd
$ sudo systemctl status smbd

  ? smbd.service - Samba SMB Daemon
  ...
  Status: "smbd: ready to serve connections..."
  ...
```

## Configurando Samba Share

Edita el archivo `smb.conf` con el comando `sudo nano /etc/samba/smb.conf`, añadiendo al final del documento lo siguiente:

```config
[media]
comment = media folder
path = /media
writeable = yes
browsable = yes
public = no
valid users = danimart1991, root
```

Adjunto una explicación de cada línea:

- `[media]`: Es el nombre corto que describe aquello que vamos a compartir. Un identificador.
- `comment`: Un comentario para describir que estamos compartiendo.
- `path`: La ruta de la carpeta que queremos compartir.
- `writeable`: Indica si queremos que la carpeta sea de solo lectura o si quieres permitir que se realizan escrituras y borrados sobre ella.
- `browsable`: Si este recurso compartido se ve en la lista de recursos compartidos disponibles en una vista de red y en la lista de navegación.
- `public`: Indica si la carpeta NO necesita contraseña para acceder a ella.
- `valid users`: Listado de usuarios que tendrán acceso a la carpeta.

> Puedes consultar más opciones en la [documentación del archivo de configuración](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html).

Guardamos el archivo con la tecla `F3` y salimos con `F2`.

## Configurando Usuarios

Como has podido ver, he indicado en el campo `valid users` que usuarios pueden acceder a los archivos. Estos usuarios han de tener acceso a la carpeta compartida y sus archivos, de lo contrario no podrán verlos cuando accedamos desde _Windows_.

Además, estos usuarios tienen que estar creados en nuestro servidor. De no ser así, basta con usar el comando:

```shell
$ sudo useradd danimart1991
```

A continuación. Tenemos que declarar ese usuario en _Samba_ y asignarle una contraseña exclusiva para este servicio. Para ello usamos el siguiente comando:

```shell
$ sudo smbpasswd -a danimart1991
New SMB password: xxxxxxxx
Retype new SMB password: xxxxxxxx
```

Finalmente reiniciamos el servicio:

```shell
$ sudo systemctl restart smbd
```

## Conclusión

Ya tenemos configurado nuestro servidor _Samba_ para su acceso desde _Windows_. Basta con abrir un _Explorador de Archivos_, escribir la dirección del servidor y acceder indicando las credenciales que hemos creado previamente.

[![Acceso Servidor desde Windows con Samba](/assets/posts/es/servidor-samba-en-debian/windows-samba.png)](/assets/posts/es/servidor-samba-en-debian/windows-samba.png)

Ahora tendrás un acceso rápido y sencillo desde tus dispositivos _Windows_ a las carpetas del servidor.

## Referencias

- Agradecimientos a José Antonio Simancas (_JASimancas_)
- [Samba](https://www.samba.org/)
- [Server World - Debian 11 Bullseye : Samba](https://www.server-world.info/en/note?os=Debian_11&p=samba&f=1)
- [Linuxito - Instalar y configurar un servidor Samba en Debian](https://www.linuxito.com/gnu-linux/nivel-basico/1233-instalar-y-configurar-un-servidor-samba-en-debian)
- [Unixcop - How to configure Samba Server with Debian 11](https://unixcop.com/how-to-configure-samba-server-with-debian-11/)
- [smb.conf — The configuration file for the Samba suite](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html)
