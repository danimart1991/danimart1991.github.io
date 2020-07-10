---
title: "IP estática en Debian"
header:
  image: /assets/posts/es/ip-estatica-en-debian/header.jpg
categories:
  - Tutorial
  - PC   
  - Linux
lang: es
ref: 35
permalink: /es/ip-estatica-en-debian/
---

Por defecto, el instalador de [*Debian 10*](https://www.debian.org/) configura la red vía [*DHCP*](https://es.wikipedia.org/wiki/Protocolo_de_configuraci%C3%B3n_din%C3%A1mica_de_host). Esto hace que la *IP* del servidor cambie con cada reinicio a no ser que se reserve su *IP* en el *Router*. En mi caso, la configuración de red de ciertos dispositivos prefiero configurarlos de manera manual en el propio dispositivo, además mi *Router* no permite reservar más de 8 *IPs* por lo que se hace inviable reservar *IPs* para todos los dispositivos de mi casa.

Vamos a ver como cambiar la configuración de red del servidor para que tenga siempre la misma *IP*. Por el momento, puedes consultar cual es la configuración actual con el comando:

```bash
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:e0:4c:0e:5f:07 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.2/24 brd 192.168.1.255 scope global enp2s0
       valid_lft forever preferred_lft forever
    inet6 fe80::2e0:4cff:fe0e:5f07/64 scope link
       valid_lft forever preferred_lft forever
```

Quédate con la interfaz cuyo `inet` cumpla con el rango habitual de *IPs* de tu red. En mi caso `enp2s0`.

## Asignando una IP estática

Una vez identificada la interfaz. Abrimos el archivo de configuración de red con el comando:

```bash
$ sudo nano /etc/network/interfaces
```

Se nos mostrará el editor de texto `nano` con el archivo más o menos de la siguiente forma:

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp2s0
iface enp2s0 inet dhcp
```

Conociendo la interfaz a modificar (en mi caso `enp2s0`), modificamos la parte de `dhcp` para añadir la configuración manual, y cambiamos `allow-hotplug` por `auto` para no tener que reiniciar el servidor con cada cambio de configuración.

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp2s0
iface enp2s0 inet static
        address 192.168.1.2
        netmask 255.255.255.0
        network 192.168.1.1
        broadcast 192.168.1.255
        gateway 192.168.1.1
```

Cambia cada campo por los datos correspondientes a tu red y tu interfaz.

A continuación, guarda el fichero con la tecla **F3** y cierra el editor de texto con **F2**.

## Modificar el servidor DNS

Si además se quiere modificar el servidor *DNS* usado por el dispositivo para la resolución *DNS*, ejecuta el siguiente comando:

```bash
$ sudo nano /etc/resolv.conf
```

En el editor de texto, añade o modifica tantas lineas `nameserver` como servidores *DNS* quieras usar. Por ejemplo:

```bash
domain Home
search Home
nameserver 1.1.1.1
nameserver 8.8.8.8
```

En mi caso, dejo que el *Router* sea el encargado de asignar el servidor *DNS* que configure en el mismo previamente, así que apunto a la puerta de enlace del *Router*:

```bash
domain Home
search Home
nameserver 192.168.1.1
```

De nuevo, guardamos con **F3** y salimos con **F2**.

## Aplicando cambios

Para aplicar los cambios ejecuta estos comandos con la interfaz que corresponda:

```bash
$ sudo ifdown enp2s0
$ sudo ifup enp2s0
```

## Comprobación

Se pueden comprobar que los cambios han sido aplicados ejecutando de nuevo el comando:

```bash
$ ip a
```

Verás que la interfaz tiene ahora asignada la *IP* configurada en pasos anteriores.

En cuanto al servidor *DNS*. Se puede ejecutar un simple comando `ping` para ver si se resuelve algún dominio:

```bash
$ ping -c3 danielmartingonzalez.com
PING danielmartingonzalez.com (185.199.109.153) 56(84) bytes of data.
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=1 ttl=57 time=11.2 ms
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=2 ttl=57 time=10.9 ms
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=3 ttl=57 time=10.8 ms

--- danielmartingonzalez.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 5ms
rtt min/avg/max/mdev = 10.796/10.941/11.157/0.177 ms
```

## Conclusión

Nuestra maquina con *Debian* ahora tendrá una **configuración de red manual** que facilitará su gestión. Para ello solo ha hecho falta modificar dos archivos y reiniciar la interfaz de red. Estos conocimientos nos serán útiles en el futuro para próximos artículos que requerirán que el propio dispositivo actúe de servidor *DNS*. Pero eso se verá más adelante.
