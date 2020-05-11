---
title: "SSH y Terminal Web en Hass.io"
header:
  image: /assets/posts/es/ssh-y-terminal-web-en-hassio/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 20
permalink: /es/ssh-y-terminal-web-en-hassio/
---

En ocasiones se deben ejecutar comandos en nuestro servidor de *Home Assistant*. Para ello, al igual que en otros muchos dispositivos, existe la opción de acceso remoto por [***SSH (Secure Shell)***](https://es.wikipedia.org/wiki/Secure_Shell). Esto nos da la opción de poder recuperar nuestro servidor ante una configuración fallida o ejecutar comandos a bajo nivel para tener un mayor control y poder realizar acciones que de otra manera serían imposibles.

[*Hass.io*](https://www.home-assistant.io/hassio/) nos brinda un [*Add-on*](https://www.home-assistant.io/addons/) que ofrece ambas cosas, crear un **servidor *SSH***, basado en [*OpenSSH*](https://www.openssh.com/), y un **Terminal Web** para poder lanzar comandos.

![Hassio Terminal Web](/assets/posts/es/ssh-y-terminal-web-en-hassio/image01.jpg)

## El ***Add-on SSH & Web Terminal***

Antes de nada, el *Add-on* [***SSH & Web Terminal***](https://github.com/hassio-addons/addon-ssh) es extremadamente poderoso, dando acceso a todas las herramientas, comandos, archivos y *hardware* del servidor. Es necesario crear una configuración segura para el *Add-on* y conocer los comandos que se van a usar para evitar posibles daños.

Aún con toda la potencia que posee el *Add-on*, la instalación y primeros usos son muy sencillos. Vamos a verlo.

## Instalación y configuración

Como en [otros artículos](/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/#instalando-y-configurando-el-add-on), la instalación pasa por acceder al menú *Hass.io* del panel izquierdo de la interfaz web de *Home Assistant* y a continuación buscar el *Add-on* ***SSH & Web Terminal*** dentro de la pestaña *Add-on Store*.

![Hassio Add-on Store](/assets/posts/es/ssh-y-terminal-web-en-hassio/image02.jpg)

Dentro, hacemos clic en ***Install***, una vez instalado, habilitamos las opciones: "*Start on boot*", "*Show in sidebar*" y "*Protection Mode*". Esta última es necesaria para dar permisos elevados al *Add-on* para ejecutar ciertos comandos.

![Configuración SSH & Web Terminal](/assets/posts/es/ssh-y-terminal-web-en-hassio/image03.jpg)

Antes de activar el *Add-on*, en la página, baja hasta la sección de configuración y pon el siguiente código:

```json
{
  "ssh": {
    "username": "USUARIOSSH",
    "password": "CONTRASEÑASSH",
    "authorized_keys": [],
    "sftp": false,
    "compatibility_mode": false,
    "allow_agent_forwarding": false,
    "allow_remote_port_forwarding": false,
    "allow_tcp_forwarding": false
  },
  "web": {
    "ssl": true,
    "certfile": "fullchain.pem",
    "keyfile": "privkey.pem"
  },
  "share_sessions": true,
  "packages": [],
  "init_commands": []
}
```

> Si no se usa protección *SSL*, desactivar el nodo `ssl` con el valor `false`, y borrar los nodos `certfile` y `keyfile`.

La configuración está preparada para un servidor configurado a partir de los artículos anteriores. Solo es necesario cambiar el usuario y contraseña de *SSH*. El resto puede obviarse a no ser que se desee [una configuración avanzada](https://github.com/hassio-addons/addon-ssh#configuration).

Guarda haciendo clic en ***Save*** y arriba del todo en la página haz clic en ***Start***. Recomendable además reiniciar el servidor de *Home Assistant* para que se apliquen todos los cambios.

## Probando

Una vez iniciado, la sección **Terminal** aparecerá en el panel lateral izquierdo. Si accedes, cargará una ventana de *Terminal* donde se podrán ejecutar comandos. De nuevo, cuidado con los comandos que vayas a ejecutar.

![Terminal Web](/assets/posts/es/ssh-y-terminal-web-en-hassio/image04.jpg)

Por último, para poder conectar de manera remota por *SSH* es necesario **abrir el puerto 22** en el *router* de conexión a *Internet* y redirigir junto con la *IP* al servidor *Home Assistant*. En la propia configuración del *Add-on* hay una sección para poder cambiar el puerto por otro.

![Configurar puerto](/assets/posts/es/ssh-y-terminal-web-en-hassio/image05.jpg)

Desde *Putty* o una terminal remota con *SSH* instalado, basta con ejecutar el comando `ssh -p 22 USUARIOSSH@TU_DOMINIO.duckdns.org` y escribir la contraseña antes configurada para acceder al servidor.

![SSH Terminal](/assets/posts/es/ssh-y-terminal-web-en-hassio/image06.jpg)

## Conclusión

A partir de ahora podrás acceder a tu servidor *Home Assistant* de manera remota y segura para ejecutar comandos, así como ejecutar comandos directamente desde la interfaz de *Home Assistant* con toda la potencia y versatilidad que esto ofrece.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
