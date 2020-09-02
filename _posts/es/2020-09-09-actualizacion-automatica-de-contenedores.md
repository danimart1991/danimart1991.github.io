---
title: "Actualización automática de contenedores"
header:
  image: /assets/posts/es/actualizacion-automatica-de-contenedores/header.png
categories:
  - Tutorial
  - Docker
  - Linux
lang: es
ref: 39
permalink: /es/actualizacion-automatica-de-contenedores/
---

Conforme ampliamos nuestro número de contenedores [*Docker*](https://www.docker.com/), se hace tedioso estar pendiente de posibles actualizaciones de las aplicaciones que usamos e incluso olvidarnos completamente de actualizar aquellas que se suelen usar en segundo plano.

**[*Ouroboros*](https://github.com/pyouroboros/ouroboros) es un contenedor que actualiza otros contenedores** a la última versión de manera recurrente para que no tengamos que hacerlo nosotros.

## *Ouroboros*

*Ouroboros* monitoriza los contenedores en ejecución (todos o solo los especificados) y cada pocos minutos revisa si hay alguna imagen actualizada para alguno de ellos y lo actualiza a la última versión. Además, conserva toda la configuración con la que se levantó el contenedor, así que no perderemos nada.

> Es posible que la nueva imagen traiga consigo un cambio de la configuración del contenedor. En estos casos la actualización fallará. Se recomienda el uso de *Ouroboros* en imágenes estables y con recorrido.

## Instalación

Aunque ***Ouroboros* guarda su configuración en un archivo [*YAML*](https://yaml.org/)** que se puede editar para configurar los diferentes aspectos de la aplicación. Una de las ventajas que posee es que se puede configurar casi todo por variables de entorno al levantar la instancia del contenedor. Así que no es necesario montar un volumen vinculado al host para persistir los datos.

Para **levantar una instancia**, basta con ejecutar el siguiente comando:

```bash
$ docker run -d --name=Ouroboros --hostname=ouroboros --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -e CLEANUP=true -e TZ='Europe/Madrid' -e INTERVAL=300 pyouroboros/ouroboros:latest
```

Voy a explicar cada apartado:

- `docker run -d`: Crea un contenedor con la siguiente configuración si no existe y lo arranca en segundo plano.
- `--name=Ouroboros`: El nombre del contenedor. Es importante pues podemos tener varios contenedores basados en la misma imagen.
- `--hostname=ouroboros`: El nombre de la máquina del contenedor. Opcional.
- `--restart=always`: Reinicia el contenedor si se para. Si se para manualmente, solo es reiniciado cuando el contenedor se reinicie manualmente o el servicio *Docker* sea reiniciado.
- `--network=host`: La red `host` utiliza la dirección *IP* del servidor, de modo que la red de un contenedor parece ser el propio servidor en lugar de estar separado.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Vincula una carpeta del servidor (izquierda), con una carpeta del contenedor (derecha). *Ouroboros* tendrá acceso a los contenedores del servidor, vincula su información con el archivo `docker.sock`.
- `-e CLEANUP=true`: Borra las imágenes antiguas después de actualizar.
- `-e TZ='Europe/Madrid'`: La zona horaria de la máquina del contenedor. Opcional. Es muy útil para que los logs y notificaciones sean con la hora local.
- `-e INTERVAL=300`: Intervalo para comprobar si existen actualizaciones. Por defecto: 5 minutos.
- `pyouroboros/ouroboros:latest`: Indica la imagen usada para montar el contenedor. Si no se indica versión cogerá la última versión estable.

**Una vez ejecutado, los contenedores empezarán a actualizarse**. En el caso de que no queramos que algún contenedor sea actualizado, se puede usar la variable de entorno `-e IGNORE="Contenedor1 Contenedor2 Conte..."`.

## Notificaciones

Por si fuera poco, *Ouroboros* incluye [*Apprise*](https://github.com/caronc/apprise) en su código, que es un **servicio de notificaciones** que incluye la mayoría de servicios y aplicaciones usados comúnmente.

Esto nos permite recibir notificaciones en la aplicación o servicio de nuestro gusto cada vez que se actualice algún contenedor. Y lo mejor, basta con incluir un par de parámetros más en el comando de *Docker*.

Por ejemplo, podemos crear un [*Bot* de *Telegram*](https://www.danielmartingonzalez.com/es/notificaciones-de-home-assistant-en-telegram/#bots-en-telegram), obtener su *API Token* y el *Chat ID* y añadirlos al comando con la variable de entorno `NOTIFIERS="tgram://TU_API_TOKEN/TU_CHAT_ID/"`.

```bash
$ docker stop Ouroboros
$ docker rm Ouroboros
$ docker run -d --name=Ouroboros --hostname=ouroboros --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -e CLEANUP=true -e TZ='Europe/Madrid' -e INTERVAL=300 -e NOTIFIERS="tgram://1234567890:1234567890abcdefghijklmnopqrstuvwxyz/0987654321/" pyouroboros/ouroboros:latest
```

**En cuanto se realice una actualización, recibiremos una notificación.**

![Notificaciones Telegram](/assets/posts/es/actualizacion-automatica-de-contenedores/notificacion-telegram.png)

Podemos incluir [todos los servicios de *Apprise*](https://github.com/caronc/apprise#supported-notifications) que queramos simplemente dejando un espacio entre uno y otro en la variable `NOTIFIERS`.

## Conclusión

Todos tus contenedores ahora estarán actualizados a la última versión sin necesidad de estar pendiente de nuevas versiones y además recibir un aviso para poder consultar las novedades que trae o si hay algún cambio de configuración que obliga a realizar algún pequeño cambio. Todo ello con un contenedor sencillo que no necesita copias de seguridad ni mantenimiento.
