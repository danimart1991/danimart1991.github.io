---
title: "Integrando un SAI en nuestro servidor"
header:
  image: /assets/posts/es/integrando-sai-servidor/header.png
  teaser: /assets/posts/es/integrando-sai-servidor/teaser.png
categories:
  - Tutorial
  - Homelab
  - Servidor
tags:
  - Homelab
  - Server
  - Tutorial
  - Sai
  - Ups
  - Usb
  - Servidor
lang: es
ref: 48
permalink: /es/integrando-sai-servidor/
---

Nuestro servidor, no deja de ser un equipo electrónico, y si bien, [hemos protegido su información por _software_ mediante copias de seguridad](https://www.danielmartingonzalez.com/es/copias-de-seguridad-para-con-docker/), lo apropiado es proteger también el _hardware_. No queremos que, por una subida de tensión, o por un simple corte de luz, los datos queden corruptos o cualquier componente electrónico quede dañado y tengamos que sustituirlo.

**Los SAI (Sistemas de Alimentación Ininterrumpida)** surgen ante esta necesidad. De manera muy resumida, son baterías que, colocadas entre nuestros dispositivos electrónicos y la corriente, no solo protege a los dispositivos de posibles problemas en el suministro eléctrico, si no que también, ante un corte, ofrecen energía un tiempo prudencial para que podamos salvaguardar los datos, apagar el dispositivo correctamente y evitar problemas.

Si bien, el objetivo de este artículo no es el de describir que es un SAI, que tipos existen, que has de buscar cuando compres uno... si no la integración de éste en nuestro servidor para poder monitorizar la información que ofrece y poder llevar a cabo distintas funciones cuando nos interese de manera automatizada. Si recomiendo, analizar que el SAI que compremos sea integrable por _USB_, ya que no todos lo son.

Aunque compatible con muchas marcas y modelos (que veremos más adelante), para este tutorial vamos a integrar el modelo [**Salicru SPS 900 ONE**](https://amzn.to/3JYuON6) haciendo uso de [**_NUT_ (_Network UPS Tools_)**](https://networkupstools.org/).

## Servidor NUT

[_NUT_ (_Network UPS Tools_)](https://networkupstools.org/) consta de dos partes bien diferenciadas, **un software en modo servidor**, el cual obtiene los datos del SAI, y **un software en modo cliente**, que muestra los datos obtenidos pidiendo al servidor esa información.

Necesitamos conocer que dispositivo _USB_ ha asignado nuestro servidor al SAI. Para ello, en la terminal del servidor que va a gestionar el SAI ejecutamos `lusb`, enchufamos un cable _USB_ del _SAI_ al servidor y lanzamos el comando de nuevo. Veremos una nueva entrada:

```bash
...
Bus 002 Device 001: ID 0665:5161 Cypress Semiconductor USB to Serial
...
```

Apuntamos _Bus_ (`002`) y _Device_ (`001`). Con esta información sacaremos la ruta del `device`. Siguiendo el ejemplo, quedaría: `/dev/bus/usb/002/001`.

A continuación, y como viene siendo habitual en el blog, [vamos a instanciar un contenedor _Docker_](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian/) con el fin de crear un servicio con el propósito que buscamos. En este caso, crear un servidor _Network UPS Tools_.

Ejecutamos el siguiente comando en un terminal:

```bash
docker run -d --name=nut-upsd --hostname=nut-upsd --restart=always --network=host --device /dev/bus/usb/002/001 -e UPS_NAME="server_ups" -e UPS_DESC="Server - Salicru SPS 900 ONE" -e UPS_DRIVER="blazer_usb" -e UPS_PORT="auto" -e API_USER="upsmon" -e API_PASSWORD="123456789ABCDEFGH" -e ADMIN_PASSWORD="123456789ABCDEFGH" -e SHUTDOWN_CMD="echo 'Home has no current. Proceeding to shut down...'" upshift/nut-upsd
```

Vamos a explicar cada uno de los parámetros que se han usado:

- `docker run -d`: Crea un contenedor con la siguiente configuración si no existe y lo arranca en segundo plano.
- `--name=nut-upsd`: El nombre del contenedor. Es importante pues podemos tener varios contenedores instanciados desde la misma imagen.
- `--hostname=nut-upsd`: Es el nombre del host que se va a usar dentro del contenedor.
- `--restart=always`: Reinicia el contenedor automáticamente si se para. Si se para manualmente, solo es inciiado cuando el contenedor se inicie manualmente o el servicio _Docker_ sea reiniciado.
- `--network=host`: La red host utiliza la dirección _IP_ del servidor, de modo que la red de un contenedor parece ser el propio servidor en lugar de estar separado.
- `--device /dev/bus/usb/002/001`: Damos acceso al contenedor al dispositivo _USB_ que hemos descubierto antes.
- `-e UPS_NAME="server_ups -e UPS_DESC="Server - Salicru SPS 900`: Es el nombre y descripción que queremos dar al SAI, por si tenemos más de uno.
- `-e UPS_DRIVER="blazer_usb"`: El _driver_ que vamos a usar con el SAI. Cada marca suele tener un _driver_ diferente. Lee más abajo.
- `-e UPS_PORT="auto"`: Indica el puerto del SAI si por un casual necesita un puerto específico. No suele ser necesario modificar este parámetro.
- `-e API_USER="upsmon" -e API_PASSWORD="123456789ABCDEFGH"`: Indica el usuario y contraseña que queremos usar para el modo de solo lectura de datos. Es el usuario que usaremos normalmente.
- `-e ADMIN_PASSWORD="123456789ABCDEFGH"`: Indica la contraseña usada en el modo administrador para realizar funciones de escritura en el servidor y en el SAI.
- `-e SHUTDOWN_CMD="echo 'Home has no current. Proceeding to shut down...'"`: _script_/comando a ejecutar cuando la batería sea MUY baja. Por ejemplo, realizar un apagado del servidor.
- `upshift/nut-upsd`: Indica la imagen usada para montar el contenedor. Si no se indica versión cogerá la última versión estable.

Para saber que `UPS_DRIVER` usar, se puede consultar [la lista de compatibilidad de _NUT_](https://networkupstools.org/stable-hcl.html), en el caso de no encontrarlo, hay una lista de [dispositivos probados por la comunidad](https://networkupstools.org/ddl/).

> También dispones de un ejemplo para _Docker Compose_ [aquí](https://github.com/danimart1991/docker-compose-files/tree/master/nut-upsd).

## Clientes

Una vez instanciado el servidor, podremos usar cualquier cliente para consultar datos o realizar diferentes funciones.

El propio contenedor del servidor incluye un cliente. Si tenemos conocimientos suficientes de _Docker_, podemos realizar un _Docker Attach_ y ejecutar `upsd <UPS_NAME>` (`UPS_NAME` es el nombre del SAI que hemos indicado al instanciar el servidor). También se pueden ejecutar comandos con [upscmd](https://networkupstools.org/docs/man/upscmd.html) y [upsrw](https://networkupstools.org/docs/man/upsrw.html).

### docker-webnut

[![Vista previa cliente docker-webnut](/assets/posts/es/integrando-sai-servidor/docker-webnut.png)](/assets/posts/es/integrando-sai-servidor/docker-webnut.png)

[**_docker-webnut_**](https://github.com/edgd1er/docker-webnut) es otro cliente sencillo para consultar datos. Podemos instanciar un contenedor _Docker_ con el siguiente comando:

```bash
docker run -d --name=webnut --hostname=webnut --restart=always --network=host -e UPS_HOST="<IP_SERVIDOR>" -e UPS_PORT="3493" -e UPS_USER="upsmon" -e UPS_PASSWORD="123456789ABCDEFGH" edgd1er/webnut:latest
```

Solo necesitamos indicar la _IP_ de la máquina a la que está conectado por _USB_ el SAI (en nuestro ejemplo, la _IP_ del servidor), el usuario y contraseña que hemos indicado al instanciar el servidor _NUT_ y el puerto, que por defecto es `3493`.

A continuación, accedemos a un navegador _Web_ e indicamos la dirección `http://<IP_Servidor>:3494`.

### nut-webui

![Vista previa cliente nut-webui](/assets/posts/es/integrando-sai-servidor/nut-webui-dashboard-01.png)](/assets/posts/es/integrando-sai-servidor/nut-webui-dashboard-01.png)

![Vista previa cliente detalle nut-webui](/assets/posts/es/integrando-sai-servidor/nut-webui-dashboard-02.png)](/assets/posts/es/integrando-sai-servidor/nut-webui-dashboard-02.png)

El cliente [**_nut-webui_**](https://github.com/gpdm/nut) es algo más complejo. Necesitamos crear previamente los archivos:

- `upsset.conf`
  - Descomentar la línea para disponer de `http://<IP_Servidor>:6544/cgi-bin/nut/upsset.cgi`
  - Usuario _admin_, contraseña, la generada automáticamente en el _Log de Docker_ o indicada en la creación del servidor _NUT_.
- `hosts.conf`
  - Poner dentro los datos del SAI.
- `upsstats.html`
- `upsstats-single.html`

> Configuración y archivos de ejemplo: [https://github.com/gpdm/nut/tree/master/nut-webui/files/etc/nut](https://github.com/gpdm/nut/tree/master/nut-webui/files/etc/nut)

E incluirlos en una carpeta que usaremos al instanciar el contenedor con el siguiente comando:

```bash
docker run -d --name=nut-webui --hostname=nut-webui --restart=always -p 6495:80 -v <RUTA_CARPETA_ARCHIVOS_CONFIGURACIÓN>:/etc/nut gpdm/nut-webui:latest
```

A continuación, accedemos a un navegador _Web_ e indicamos la dirección `http://<IP_Servidor>:3495`.

## Bonus: Integración en Home Assistant

Podemos usar [**_Home Assistant_**](https://www.home-assistant.io/) como cliente, monitorizar los valores que ofrece el servidor y realizar diferentes automatizaciones dependiendo del estado de los sensores creados con estos valores.

Seguramente, el sistema de detección automático de _Home Assistant_ detecte _NUT_ en nuestra red, pero si no es el caso, basta con añadir la integración desde `Ajustes -> Dispositivos y Servicios -> Añadir Integración -> Network UPS Tools (NUT)`.

[![Configuracion de NUT en Home Assistant](/assets/posts/es/integrando-sai-servidor/nut-homeassistant-configuracion.png)](/assets/posts/es/integrando-sai-servidor/nut-homeassistant-configuracion.png)

Indicamos los datos de nuestro servidor _NUT_ como hemos hecho en otros clientes y listo.

[![Sensores de SAI en Home Assistant](/assets/posts/es/integrando-sai-servidor/nut-homeassistant-sensores.png)](/assets/posts/es/integrando-sai-servidor/nut-homeassistant-sensores.png)

## Bonus 2: Integración en CheckMK

Aunque no hemos hablado en este blog sobre [**_CheckMK_**](https://checkmk.com/), es un sistema de monitorización y alertas que uso con frecuencia. Si estás familiarizado con él, puede interesarte el siguiente _Plugin_ para poder monitorizar los datos del SAI: https://github.com/danimart1991/checkmk_nut

[![Sensores de SAI en CheckMK](/assets/posts/es/integrando-sai-servidor/nut-checkmk-sensores.png)](/assets/posts/es/integrando-sai-servidor/nut-checkmk-sensores.png)

## Referencias

- [Random Bits - Monitoring a UPS with nut on Debian or Ubuntu Linux](https://blog.shadypixel.com/monitoring-a-ups-with-nut-on-debian-or-ubuntu-linux/)
- [Domology - Integrar un SAI en Home Assistant](https://domology.es/integrar-un-sai-en-home-assistant/)
- [Network UPS Tools - Documentation](https://networkupstools.org/documentation.html)
- [Tinkerings - UPS monitoring with NUT, Nagios and Check_MK](https://blog.minodudd.com/2013/10/23/ups-monitoring-with-nut-nagios-and-check_mk/)
- [Professional Review - SAI: Qué es, para qué sirve y que tipos hay en el mercado](https://www.profesionalreview.com/2019/02/23/que-es-sai/)
