---
title: "De Debian 10 a Debian 11"
header:
  image: /assets/posts/es/de-debian10-a-debian11/header.png
categories:
  - Tutorial
  - Debian
tags:
  - Servidor
  - Debian
  - Tutorial
  - Actualizaciones
lang: es
ref: 45
permalink: /es/de-debian10-a-debian11/
---

[_Debian 11_ (_bullseye_)](https://www.debian.org/News/2021/20210814) lleva ya varios meses disponible en el canal estable para su uso. Esta versión contiene un total de 59.551 paquetes de los que más de 11.294 son nuevos, y esto a pesar de una reducción significativa de más de 9.519 paquetes que se marcaron como obsoletos y fueron eliminados. 42.821 paquetes se han actualizado y 5.434 permanecen sin modificar. Es decir, una gran cantidad de mejoras y solución de fallos que mejorarán nuestro servidor.

**Hoy vamos a ver como actualizar nuestro sistema de _Debian 10_ a _Debian 11_ en pocos pasos.**

## Copia de seguridad del sistema actual

Lo primero y más importante es **tener una copia de seguridad del sistema**. Con la actualización no se debería romper nada, pero por si acaso. [Vamos a realizar una copia de seguridad con _Duplicati_](https://www.danielmartingonzalez.com/es/copias-de-seguridad-para-con-docker/).

## Actualizando los paquetes actuales

**Actualiza los paquetes existentes en _Debian 10_** para tenerlos a la última versión usando los siguientes comandos:

```shell
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
$ sudo apt-get dist-upgrade -y
$ sudo apt-get autoremove -y
$ sudo apt-get clean -y
$ sudo reboot
```

## Modificando los repositorios

**Modifica la lista de repositorios del sistema** con el comando `sudo nano /etc/apt/sources.list` sustituyendo los que contengan la palabra `buster` por los siguientes:

```
deb http://deb.debian.org/debian/ bullseye main contrib non-free
deb-src http://deb.debian.org/debian/ bullseye main contrib non-free

deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb-src http://security.debian.org/debian-security bullseye-security main contrib non-free

deb http://deb.debian.org/debian/ bullseye-updates main
deb-src http://deb.debian.org/debian/ bullseye-updates main
```

## (Opcional) Actualizar key de Docker

Si tenemos instalado _Docker_, es recomendable actualizar la _GPG key_ con los siguientes comandos:

```shell
$ sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ sudo echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Si pide sobrescribir, confirmamos.

## Actualizando a Debian 11

Por fin, **ejecuta los siguientes comandos para actualizar tu sistema a _Debian 11_**.

```shell
$ sudo apt-get update -y
$ sudo apt-get full-upgrade -y
$ sudo reboot
```

> Durante la instalación, conservar las configuraciones locales en caso de que nos pida realizar cambios (suele ser la opción por defecto).

## Actualizando y limpiando paquetes

Por último, **actualizamos todos los paquetes instalados** y realizamos una limpieza de paquetes obsoletos con los siguientes comandos:

```shell
$ apt-get update -y
$ apt-get upgrade -y
$ apt-get dist-upgrade -y
$ apt-get autoremove -y
$ apt-get clean -y
$ sudo reboot
```

Repetimos esta lista de comandos nuevamente hasta que el resultado de cada uno indique que no se tienen que instalar, actualizar y/o borrar más paquetes.

## Conclusión

Podemos comprobar la versión de _Debian_ instalada en el sistema con el comando `lsb_release -a`.

```
Distributor ID: Debian
Description:    Debian GNU/Linux 11 (bullseye)
Release:        11
Codename:       bullseye
```

Siguiendo el listado de comandos de este tutorial nuestro servidor estará actualizado a _Debian 11_ y tendremos soporte para 5 años de manera estable. Recuerda mantener los paquetes actualizados para evitar distintos fallos y tener un sistema sano.

## Referencias

- Agradecimientos a José Antonio Simancas (_JASimancas_)
- [Cybercity - How to upgrade Debian 10 to Debian 11 Bullseye using the CLI](https://www.cyberciti.biz/faq/update-upgrade-debian-10-to-debian-11-bullseye/)
- [Docker Docs - Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/)
