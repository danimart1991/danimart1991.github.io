---
title: "Webmin: Administrando un servidor desde el navegador"
header:
  image: /assets/posts/es/webmin-administrando-un-servidor-desde-navegador/header.png
  teaser: /assets/posts/es/webmin-administrando-un-servidor-desde-navegador/teaser.png
categories:
  - Tutorial
  - Server
tags:
  - Webmin
  - Server
  - Tutorial
  - Administración
lang: es
ref: 47
permalink: /es/webmin-administrando-un-servidor-desde-navegador/
---

No conozco mucho de sistemas _Unix_. Si bien puedo teclear algunos comandos y moverme por sus directorios, no es algo a lo que esté acostumbrado ni con lo que me guste lidiar. Pero reconozco, que una distribución _Linux_ es la mejor opción para montar un servidor.

[![Pantalla de inicio de información del sistema en Webmin (Dashboard)](/assets/posts/es/webmin-administrando-un-servidor-desde-navegador/webmin-dashboard.png)](/assets/posts/es/webmin-administrando-un-servidor-desde-navegador/webmin-dashboard.png)

[**_Webmin_**](https://webmin.com/) resuelve la gestión de un servidor de manera elegante, ya que se trata de una herramienta de configuración de sistemas accesible vía web para sistemas _Unix_. Con ella, puedes desde la comodidad del navegador configurar cuentas de usuario, visualizar y editar archivos, ver el estado de los recursos del sistema y un largo etcétera. Este aplicativo nos ayuda a eliminar la necesidad de editar los archivos de configuración y de los servicios manualmente y brinda una interfaz para hacerlo de manera sencilla y con pequeños consejos.

> **AVISO**: _Webmin_ permite modificar configuraciones y datos a nivel de núcleo del sistema, esto significa que antes de tocar cualquier módulo, conviene leer documentación acerca de cómo funciona y en que puede afectar a nuestro servidor.

## Instalación y Uso

Vamos a instalar _Webmin_ en el propio sistema por paquete `apt`. La razón de hacerlo así y no en un contenedor _Docker_, es porque _Webmin_ necesita tener acceso completo a todo el sistema, tanto a archivos como a servicios. Además, si por cualquier motivo el servicio _Docker_ cae, podremos seguir usando _Webmin_ para solucionar el problema.

Suponiendo que nuestro servidor tiene una distribución basada en _Debian_, _Ubuntu_ o _Mint_. Ejecutamos los siguientes comandos:

```bash
$ sudo wget -qO - http://www.webmin.com/jcameron-key.asc | sudo apt-key add -
$ sudo sh -c 'echo "deb http://download.webmin.com/download/repository sarge contrib" > /etc/apt/sources.list.d/webmin.list'
$ sudo apt install apt-transport-https -y
$ sudo apt update
$ sudo apt install webmin -y
```

> Para el resto de distribuciones y tipos de instalación disponibles puedes consultar [esta _Wiki_](http://doxfer.webmin.com/Webmin/Installation).

Y poco más, por último, abrimos un navegador y accede a la dirección `https://<ip_de_tu_server>:10000`. En la pantalla principal (_Dashboard_) tendrás información resumida del estado de tu servidor. En la barra de la izquierda dispondrás de distintas utilidades para gestionar el servidor. Recuerda, antes de tocar, conviene leer la documentación de ese módulo y herramienta.

> Es posible que, al entrar por primera vez, se muestre un error de certificados, acepta entrar de manera no segura.

## Referencias

- [Webmin - Official Web](https://webmin.com)
- [Webmin - Wiki](https://doxfer.webmin.com/Webmin)
- [Raiola Networks - Manual completo Webmin, Usermin y Virtualmin](https://raiolanetworks.es/blog/webmin-usermin-virtualmin)
- [Linuxize - How to Install Webmin on Debian 10 Linux](https://linuxize.com/post/how-to-install-webmin-on-debian-10/)
