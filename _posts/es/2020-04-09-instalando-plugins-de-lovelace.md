---
title: "Instalando Plugins de Lovelace"
header:
  image: /assets/posts/es/instalando-plugins-de-lovelace/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 31
permalink: /es/instalando-plugins-de-lovelace/
---

Es un buen momento para aprender a ampliar más si cabe el ecosistema de [*Home Assistant*](https://www.home-assistant.io/) mediante el uso de *Plugins* de [*Lovelace*](https://www.home-assistant.io/lovelace/). Estos *Plugins* extienden nuestros *Dashboards* con tarjetas personalizadas, entidades, filas y diversas opciones.

Aprovechando [el artículo anterior](/es/precios-de-tarifas-pvpc-en-home-assistant/) en el que vimos como integrar la tarifa de luz [*PVPC* (Precio Voluntario al Pequeño Consumidor)](https://www.esios.ree.es/es/pvpc), vamos a mostrar la información de la integración en una tarjeta *Lovelace* personalizada específica que he hecho.

> Muchos *Plugins* pueden instalarse de manera más sencilla usando [*HACS*](https://hacs.xyz/), a continuación, se detalla el proceso de instalación manual.

## Descargar el Plugin

Lo primero es conseguir el *Plugin*. Normalmente es un archivo de tipo *JavaScript* (`*.js`), en nuestro ejemplo `pvpc-hourly-pricing-card.js`.

Para ello, accede a la página del repositorio donde se encuentre el desarrollo de la tarjeta, típicamente [*GitHub*](https://github.com). En el caso de ejemplo:
[https://github.com/danimart1991/pvpc-hourly-pricing-card](https://github.com/danimart1991/pvpc-hourly-pricing-card).

### Alternativa 1

Una vez dentro muchos desarrolladores realizan despliegues cuando consideran que tienen una versión estable y con cambios importantes. Esta opción estará disponible desde la sección ***Releases***.

![GitHub Releases](/assets/posts/es/instalando-plugins-de-lovelace/github-releases.jpg)

Desde la página de Releases se pueden descargar diferentes versiones de los ficheros *JavaScript*.

![Página GitHub Releases](/assets/posts/es/instalando-plugins-de-lovelace/github-releases-detail.jpg)

### Alternativa 2

En otras ocasiones, tendremos que buscar el archivo *JavaScript* por el repositorio.

![Repositorio GitHub](/assets/posts/es/instalando-plugins-de-lovelace/github-file-search.jpg)

Una vez dentro de la página de detalle del archivo, buscamos el botón ***RAW***, click derecho sobre él, y presionamos "*Guardar archivo como...*".

![Descarga archivo GitHub](/assets/posts/es/instalando-plugins-de-lovelace/github-file-download.jpg)

## Instalación

Coloca el fichero dentro de la carpeta de tu elección siempre que esté dentro de la carpeta `/config/www/`. Por ejemplo `/config/www/pvpc-hourly-pricing-card.js` o `/config/www/pvpc-hourly-pricing-card/pvpc-hourly-pricing-card.js`.

### En Home Assistant 0.107 o superior

Está disponible la instalación visual mediante el menú *Configuración -> Paneles de Control Lovelace -> Recursos*.

![Sección Recursos](/assets/posts/es/instalando-plugins-de-lovelace/pantalla-recursos.jpg)

Haz click en el botón añadir (***+***) y rellena los datos. La *url* es la dirección donde has colocado el archivo sustituyendo `/www/` por `/local/`. Añade después de la dirección: `?v=0.0.1`.

![Crear Recursos](/assets/posts/es/instalando-plugins-de-lovelace/crear-recurso.jpg)

La alternativa es instalar el *Plugin* incluyéndolo dentro del nodo `lovelace` en el archivo `configuration.yaml`.

```yaml
lovelace:
  resources:
    - url: /local/plugins/pvpc-hourly-pricing-card.js?v=0.0.1
      type: module
```

Actualiza la página en tu navegador.

### En versiones anteriores a Home Assistant 0.107

Si estás usando *Lovelace* en modo Visual. Puedes incluir los recursos desde el menú edición (arriba a la derecha) y una vez más, desde el mismo punto, entrar en la opción "*Editar configuración RAW*".

```yaml
title: "Home"
resources:
  - url: /local/pvpc-hourly-pricing-card.js?v=0.0.1
    type: module
views:
  ...
```

Si estás usando *Lovelace* en modo *YAML*, solo tienes que añadir el nodo `resources` en el archivo `ui-lovelace.yaml`.

## Versiones

Quizás hayas visto como después de cada ruta de archivo incluyo el texto `?v=0.0.1`. Se ha de incluir la versión del despliegue para evitar que con cada cambio el navegador mantenga en cache el archivo JavaScript anterior. Es decir, cuando el desarrollador actualice el *Plugin*, será necesario descargar y sustituir el archivo antiguo en el servidor. Al hacer esto, es posible que muchos navegadores o incluso la aplicación de *Home Assistant* use el archivo guardado en el cache local y no la nueva versión, para forzar el uso del nuevo archivo, el truco es cambiar la versión cuando indiquemos la ruta. No importa que no sea real, solo que sea distinta cada vez.

## Usando el Plugin

Este es un punto que depende mucho del *Plugin* instalado. Es el momento de leer la documentación disponible y seguir los pasos.

![Ejemplo tarjeta](/assets/posts/es/instalando-plugins-de-lovelace/ejemplo-tarjeta.jpg)

## Conclusión

Ahora podrás ampliar fácilmente las funcionalidades y la interfaz de *Home Assistant* y como es el caso, usar tarjetas específicas para sacar todo el partido a las distintas integraciones.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
