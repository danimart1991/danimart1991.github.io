---
title: "Controla tu casa desde cualquier sitio con DuckDNS"
header:
  image: /assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/header.png
tags: homeassistant hassio domotica
lang: es
ref: 15
permalink: /es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/
last_modified_at: 2020-09-04
---

Ya hemos [creado nuestro servidor **Home Assistant**](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/), y [conocido un poco más de cerca como funciona](/es/conociendo-home-assistant/). Pero hacer una casa inteligente no tiene mucho sentido si solo podemos controlarla desde dentro de la propia casa.

[*Home Assistant*](https://www.home-assistant.io/hassio/) ofrece un [*Add-on*](https://www.home-assistant.io/addons/) muy sencillo para poder acceder a [*Home Assistant*](https://www.home-assistant.io/) desde cualquier dispositivo con *Internet* alrededor del Mundo. Además este *Add-on* incluye configuración de certificados de encriptación *SSL* para hacer esta conexión segura.

## *Duck DNS* y *Let's Encrypt*

![Duck DNS + Let's Encrypt](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/duckdns-letsencrypt.png)

[**Duck DNS**](https://www.duckdns.org/) es un servicio gratuito de redirección de *DNS* (sub-dominios de *duckdns.org*) a una *IP* de nuestra elección. Esto es, crear un subdominio `xxxxxx.duckdns.org` que al acceder desde cualquier navegador, redireccione a la IP que se indique; en nuestro caso, a la *IP* y puerto de nuestro servidor *Home Assistant*. De tal manera, que desde cualquier dispositivo con *Internet*, dentro o fuera de nuestra casa, podamos acceder a nuestro servidor e interfaz web de *Home Assistant*.

[**Let's Encrypt**](https://letsencrypt.org/) por su parte es una Autoridad de Emisión de Certificados ofrecida por *Internet Security Research Group* que es capaz de emitir certificados *SSL* de manera abierta, automática y gratis. De esta manera, nos brindará el certificado necesario para que la conexión creada por la redirección *DNS* de *Duck DNS* sea segura. No es obligatorio pero si muy recomendable usar estos certificados para añadir una capa más de protección al entorno de red.

## Requisitos

Para poder instalar y usar **Duck DNS** y **Let's Encrypt**, se debe:

- Tener una *IP* interna estática para el servidor *Home Assistant*. Esto se puede conseguir de dos formas:
  - Configurando la red de nuestra instalación de *Home Assistant* para que tenga una *IP* estática (forma que se vió en [el primer artículo](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/)).
  - Reservando la *IP* en el router principal para el dispositivo / *host* que se está usando para el servidor.

- Redireccionar en el router el puerto **8123 (*TCP*) interno** para la *IP* estática del paso anterior, al puerto que quieras **(puede ser 8123) externo**. Si no sabes bien como redireccionar los puertos, en [***Port Forward***](https://portforward.com/) existen guías para casi cualquier router conocido.

  > Apunta la ***IP*** y el **puerto externo** elegido, los necesitarás luego.

- Regístrarse en **duckdns.org** mediante cualquiera de los métodos disponibles y crear un subdominio con el nombre que quieras.

  ![Página inicio Duck DNS](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/duckdns-pagina-inicio.png)

  > Apunta el **Token** que se muestra en la página y el subdominio, también los necesitarás.

## Instalando y configurando el *Add-on*

La instalación y configuración se realizará del mismo modo que en [otros artículos](/es/configura-home-assistant-editando-sus-archivos/).

Dirígete al menú ***Supervisor -> Add-on Store*** y busca **Duck DNS**.

![Add-on Store - Duck DNS](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/ha-addon-store.png)

Dentro, verás una breve descripción del *Add-on*. Haz click en **Install**.

![Duck DNS Add-on](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/duckdns-addon.png)

Después de unos minutos, baja a la sección **Config** y rellena el cuadro de texto con los siguientes parámetros (usando el **Token y dominio** creados en los requisitos):

```json
{
  "lets_encrypt": {
    "accept_terms": true,
    "certfile": "fullchain.pem",
    "keyfile": "privkey.pem"
  },
  "token": "TU_TOKEN_DE_DUCK_DNS",
  "domains": [
    "TU_DOMINIO.duckdns.org"
  ],
  "seconds": 300
}
```

> Marcar el campo `accept_terms` a `true` habilitará la configuración y renovación de certificados SSL con Let's Encrypt.

Asegurate de que los campos token y nombre de dominio están entre comillas. Haz click en **Guardar**.

![Configurator - configuration.yaml](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/fileeditor-configuration-yaml.png)

A continuación edita el archivo `configuration.yaml`, por ejemplo con el [*Add-on File Editor*](/es/configura-home-assistant-editando-sus-archivos/), y modifica el nodo `http` y `homeassistant` con el siguiente código (si no existe, créalo):

```yaml
homeassistant:
  external_url: https://TU_DOMINIO.duckdns.org:PUERTO_EXTERNO
  internal_url: http://DIRECCION_IP_FIJA_DEL_SERVIDOR:PUERTO_EXTERNO

http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem
```

Guarda.

Vuelve a la página del *Add-on DuckDNS* (*Supervisor -> Dashboard -> Duck DNS*) y asegúrate que **Start on boot** esté activado. Del mismo modo, que el *Add-on* esté iniciado, en caso contrario haz click en **Start**.

![Duck DNS Add-on settings](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/duckdns-configuracion-addon.png)

Abajo del todo en la sección **Log**, tendrás toda la información para ver si todo ha ido bien.

## Probando

Recomendable reiniciar la instancia de *Home Assistant*, para ello, dirígete a **Configuración -> Controles del Servidor -> Administración del servidor -> Reiniciar**.

![Home Assistant Reinicio](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/reiniciar-home-assistant.png)

> En las últimas versiones de *Home Assistant* es posible que no tengas disponible esta opción hasta hacer click en tu usuario y **habilitar el modo avanzado**.

A continuación, abre el navegador y accede a **https://TU_DOMINIO.duckdns.org:8123** (con el puerto que hayas configurado en los requisitos). Deberías acceder a la página de inicio de sesión de la interfaz web de tu servidor *Home Assistant*. Introduce tus credenciales y ya podrás acceder desde cualquier dispositivo y punto del Mundo a tu casa.

> En caso de no haber configurado Let's Encrypt, la dirección será usando `http` en lugar de `https`.

![Home Assistant Resumen](/assets/posts/es/controla-tu-casa-desde-cualquier-sitio-con-duckdns/home-assistant-resumen-lovelace.png)

Otra forma de asegurar que funciona correctamente es acceder a *duckdns.org* y comparar la *IP* que se muestra para el subdominio, con la *IP* que muestran páginas como [**cualesmiip.com**](http://cualesmiip.com/); deben coincidir.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
