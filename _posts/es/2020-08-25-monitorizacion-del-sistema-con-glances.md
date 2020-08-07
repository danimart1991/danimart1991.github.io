---
title: "Monitorización del sistema con Glances"
header:
  image: /assets/posts/es/monitorizacion-del-sistema-con-glances/header.jpg
categories:
  - Tutorial
  - PC   
  - Linux
lang: es
ref: 38
permalink: /es/monitorizacion-del-sistema-con-glances/
---

[Una vez familiarizados con ***Docker***](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian/) y después de haber creado varios contenedores, es el momento idóneo para **monitorizar el servidor**. Controlaremos los recursos y veremos si algún contenedor está usándolos más de lo debido.

[***Glances***](https://nicolargo.github.io/glances/) es una herramienta de monitorización escrita en *Python* que te dará la información justa pero crítica del sistema en la propia terminal o en un navegador *Web*.

Vamos a ver dos maneras de instalar *Glances*.

## Instalación y uso

### Instalación con Script

Se puede instalar *Glances* de una manera sencilla como software del servidor usando el siguiente *Script*:

```bash
$ curl -L https://bit.ly/glances | /bin/bash
```

Para usarlo, solo tienes que ejecutar el siguiente comando:

```bash
$ glances
```

![Glances Terminal](/assets/posts/es/monitorizacion-del-sistema-con-glances/glances-terminal.png)

Este método es el más fácil, pero *Glances* y sus dependencias se quedarán instaladas en el sistema, lo que hará más difícil su desinstalación o actualización. **Recomiendo su instalación usando un contenedor *Docker***.

### Instalación en Docker

Aunque pueda parecer más complicado, si ya tenemos las nociones básicas de *Docker*, la creación del contenedor de *Glances* es bastante sencilla, basta con ejecutar el siguiente comando:

```bash
$ docker run -d --name=Glances --hostname=Glances --restart="always" --network=host -e GLANCES_OPT="-w" -v glances_config:/glances/conf -v /var/run/docker.sock:/var/run/docker.sock:ro --pid host docker.io/nicolargo/glances
```

Como es un comando con muchos argumentos, voy a explicar cada uno por separado:

- `docker run -d`: Crea un contenedor con la siguiente configuración si no existe y lo arranca en segundo plano.
- `--name=Glances`: El nombre del contenedor. Es importante pues podemos tener varios contenedores basados en la misma imagen.
- `--hostname=Glances`: El nombre de la máquina. Opcional.
- `--restart=always`: Reinicia el contenedor si se para. Si se para manualmente, solo es reiniciado cuando el contenedor se reinicie manualmente o el servicio *Docker* sea reiniciado.
- `--network=host`: La red `host` utiliza la dirección *IP* del servidor, de modo que la red de un contenedor parece ser el propio servidor en lugar de estar separado.
- `-e GLANCES_OPT="-w"`: Con este argumento *Glances* iniciará el servidor *Web* para poder ver la información desde cualquier navegador *Web*.
- `-v glances_config:/glances/conf`: Vinculamos la configuración de *Glances* a un volumen para que los datos persistan aunque borremos el contenedor.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Vincula una carpeta del servidor (izquierda), con una carpeta del contenedor (derecha). *Glances* tendrá acceso a los contenedores del servidor, vincula su información con el archivo `docker.sock`.
- `--pid host`: Indicamos que utilice el espacio de nombres del *Host* para los procesos. Este parámetro le da acceso a los procesos del servidor.
- `docker.io/nicolargo/glances`: Indica la imagen usada para montar el contenedor. Si no se indica versión cogerá la última versión estable.

A continuación, al haber utilizado la opción `-e GLANCES_OPT="-w"` se creará un servidor *Web* para la consulta de los datos en el puerto `61208`. Entra en la dirección *Web* `http://IPDELSERVIDOR:61208` y verás toda la información del sistema y los contenedores.

![Glances Web](/assets/posts/es/monitorizacion-del-sistema-con-glances/glances-web.png)

## Bonus: Integración en Home Assistant

Para evitar estar consultando *Glances* en terminal o la página Web cada cierto tiempo, podemos [integrar *Glances* en *Home Assistant*](https://www.home-assistant.io/integrations/glances/) y de paso, integrarlo en su sistema de notificaciones y recibir avisos si algún sensor sobrepasa los límites establecidos.

El único requisito es haber iniciado *Glances* con el servidor *Web*, es decir con el parámetro `-w`.

```bash
$ sudo glances -w
Glances web server started on http://0.0.0.0:61208/
```

> Al crear el contenedor *Docker* en la sección anterior, ya se ha incluido este parámetro.

En *Home Assistant*, incluimos la integración desde `Configuración -> Integraciones -> Añadir integración (Botón +)`. Busca *Glances* y configura los campos en la siguiente ventana. Si has seguido el artículo, y *Home Assistant* está instalado en la misma máquina que *Glances*, no hace falta cambiar nada.

![Integración Glances](/assets/posts/es/monitorizacion-del-sistema-con-glances/home-assistant-glances.png)

**Se creará una integración con varias entidades** que podrás incluir en el *Dashboard* o usar en automatizaciones.

## Conclusión

*Glances* puede no ser el software de monitorización del sistema más potente, pero ofrece la información más crítica para detectar problemas de un vistazo. Su instalación es apenas un comando y no consume apenas recursos.
