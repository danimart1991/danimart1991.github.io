---
title: "Configura Home Assistant editando sus archivos"
header:
  image: /assets/posts/es/configura-home-assistant-editando-sus-archivos/header.png
tags: homeassistant hassio domotica
lang: es
ref: 14
permalink: /es/configura-home-assistant-editando-sus-archivos/
last_modified_at: 2020-09-01
---

Aunque muchas de las configuraciones e integraciones de [*Home Assistant*](https://www.home-assistant.io) se pueden hacer y configurar sin necesidad de editar ficheros de ningún tipo, otros requieren de al menos unas pocas lineas de código para que funcionen correctamente.

Por eso, no está de más tener y conocer alguna herramienta que permita modificar los ficheros. Una de las más sencillas y poderosas es el [***Add-on File Editor***](https://www.home-assistant.io/addons/configurator) para [*Supervisor*](https://www.home-assistant.io/hassio/).

## Instalación

Dirígete al menú **Supervisor** ➡ [**Add-on Store**](https://www.home-assistant.io/addons) y busca [**File Editor**](https://www.home-assistant.io/addons/configurator).

![Add-on Store](/assets/posts/es/configura-home-assistant-editando-sus-archivos/hassio-addon-store.png)

No requiere configuración si no queremos hacer un uso avanzado, por el momento, simplemente haz clic en ***Install***.

Una vez terminado, nos debemos asegurar que las opciones de **activar al inicio** y **mostrar en la barra lateral** estén activas.

![Configuración File Editor](/assets/posts/es/configura-home-assistant-editando-sus-archivos/fileeditor-addon.png)

Reinicia el servidor *Home Assistant* desde **Configuración** ➡ **Controles del servidor** ➡ **Administración del servidor** ➡ **Reiniciar**

![Reiniciar HASS](/assets/posts/es/configura-home-assistant-editando-sus-archivos/reiniciar-hassio.png)

## Usando *File Editor*

Aparecerá el botón **File Editor** en la barra lateral, al seleccionarlo se abrirá una nueva página con un editor, un selector de *snippets*, y los botones de configuración, búsqueda y explorador de archivos.

![File Editor](/assets/posts/es/configura-home-assistant-editando-sus-archivos/fileeditor.png)

Si hacemos clic en el **explorador de archivos**, podremos abrir un archivo de configuración y editarlo desde el editor. Por defecto, el explorador de archivos se abre en la carpeta `/config/` que es donde se encuentra la configuración de *Home Assistant*.

Busca el archivo `configuration.yaml` y haz clic, se abrirá en el editor.

![File Editor abrir configuration.yaml](/assets/posts/es/configura-home-assistant-editando-sus-archivos/fileeditor-carpetas.png)

> Antes de comenzar, cabe destacar que el cambio de ciertos archivos de configuración hace que se desactiven métodos automáticos en *Home Assistant* como la interfaz [*Lovelace*](https://www.home-assistant.io/lovelace/) o ciertos parámetros de la pestaña de configuración. Los usuarios más avanzados prefieren usar los archivos de configuración, pues ganan mayor control y sencillez para realizar copias de seguridad, además de ser una manera muy cómoda de compartir configuraciones con la comunidad.

En nuestra primera aproximación, vamos a añadir una entidad de tipo `weather` que hace referencia a [**OpenWeatherMap**](https://openweathermap.org). Por defecto, *Home Assistant* trae una entidad `weather` que hace uso del [*NRK (Instituto Noruego de Meteorología)*](https://www.home-assistant.io/components/met/), que no funciona muy bien para localizaciones de fuera de Noruega.

Lo primero que debes hacer es ir a la web de [*OpenWeatherMap*](https://openweathermap.org/price) y pedir un *API key* que usaremos en la configuración de nuestra entidad. Regístrate y accede al plan gratuito que nos dará el *API key*.

![OpenWeather API key](/assets/posts/es/configura-home-assistant-editando-sus-archivos/openweather.png)

Con el editor abierto, en el archivo `configuration.yaml`, pega el siguiente código; cambiando `TU_OPENWEATHERMAP_API_KEY` por el *API key* que acabas de obtener.

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: TU_OPENWEATHERMAP_API_KEY
```

El orden de cada entidad creada en este archivo es a tu gusto, irás acomodándolo y creando agrupaciones según vayas incluyendo más líneas. Lo que si es importante, son los espaciados, ya que [`yaml`](https://yaml.org/) es un lenguaje que tiene en cuenta la sangría del código.

Una vez editado el fichero, haz clic en **Guardar**.

![Guardar configuration.yaml](/assets/posts/es/configura-home-assistant-editando-sus-archivos/guardar-configuration-yaml.png)

Para que se recargue la configuración, es necesario reiniciar *Home Assistant*, una manera sencilla, sin tener que ir a la pestaña *Configuración* de la barra lateral, es hacer clic en la configuración de ***File Editor*** y hacer clic en ***Restart HASS***.

> Este menú ofrece multitud de opciones interesantes como listado de iconos, listado de componentes compatibles con *Home Assistant*, consola de comandos *Shell*...

![Reiniciar HASS](/assets/posts/es/configura-home-assistant-editando-sus-archivos/reiniciar-hass-configurator.png)

Espera unos minutos, al tener [*Lovelace*](https://www.home-assistant.io/lovelace/) en modo automático, al entrar en la pestaña *Resumen*, deberías ver la tarjeta de [**OpenWeatherMap**](https://www.home-assistant.io/lovelace/weather-forecast/) con la información de la localización de tu casa (puede cambiarse desde el menú configuración de *Home Assistant*).

![Home Assistant Resumen](/assets/posts/es/configura-home-assistant-editando-sus-archivos/home-assistant-lovelace-resumen.png)

> Para borrar la entidad [***met.no***](https://www.home-assistant.io/components/met/), dado que ya no nos interesa, y como ha sido registrada mediante una [integración de manera visual](https://www.home-assistant.io/getting-started/onboarding/), ves al menú *Configuración* ➡ *Integraciones de Home Assistant*, selecciona la integración y haz clic en borrar. Reinicia de nuevo, y ya solo debería aparecer la tarjeta de [**OpenWeatherMap**](https://www.home-assistant.io/lovelace/weather-forecast/).

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
