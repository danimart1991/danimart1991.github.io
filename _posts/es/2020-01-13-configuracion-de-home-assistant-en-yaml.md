---
title: "Configuración de Home Assistant en YAML"
header:
  image: /assets/posts/es/configuracion-de-home-assistant-en-yaml/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 27
permalink: /es/configuracion-de-home-assistant-en-yaml/
---

Aunque versión a versión de [*Home Assistant*](https://www.home-assistant.io/) se ve una tendencia creciente a que todo se integre de manera visual y, con ello, cualquier usuario sin conocimientos de programación pueda usar y [configurar *Home Assistant*](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/), la realidad es que aún hoy es necesario tocar ciertos archivos para configurar algunos de los parámetros más usados y para tener un control avanzado del sistema.

Mi recomendación es intentar mover todo a archivos de configuración manual *YAML* siempre que sea posible. Nos aseguramos una gestión más centralizada, poder realizar copias de seguridad simplemente copiando el archivo o su contenido, y poder compartir configuraciones con otros usuarios de manera sencilla y segura.

Como contrapunto, en muchos casos se pierde la opción de realizar la configuración visual de las partes que se pasen a *YAML*.

## Conocimientos necesarios

El lenguaje usado para realizar la mayoría de configuraciones es *YAML*. El primer paso es aprender a utilizar el lenguaje, y nada mejor que la [hoja de especificaciones oficiales](https://yaml.org/spec/1.2/spec.html) para ello.

Por otro lado, hace falta un método de edición de archivos *YAML* que se encuentren en nuestro servidor. En el artículo "[modificación de la configuración de *Home Assistant* editando sus archivos](/es/configura-home-assistant-editando-sus-archivos/)" se puede ver como instalar el [*Add-on Configurator*](https://www.home-assistant.io/addons/configurator/) y como empezar a editar archivos.

> En entornos más avanzados, será necesario aprender lenguajes como [*Jinja2*](https://palletsprojects.com/p/jinja/) para la realización de *Templates* o [*JSON*](https://json.org/) para ciertas configuraciones externas.

## Configuraciones a YAML

Entrando en un terreno algo más pantanoso, nos encontramos con diferentes tipos de parámetros, diferentes sitios donde se guardan estos parámetros y varios métodos para pasar toda la configuración de *Home Assistant* a archivos *YAML*. Por partes, vamos a ver cómo se comportan las diferentes opciones que ya hemos podido editar de manera visual:

- **Automatizaciones**. Las [automatizaciones](https://www.home-assistant.io/integrations/automation/) se guardan directamente en la carpeta raíz en el archivo `automations.yaml`. Puede ser editado para crear nuestras automatizaciones o usar el editor visual. La diferencia es que editando el archivo se pueden [crear automatizaciones mucho más potentes](https://www.home-assistant.io/docs/automation/templating/), por contra, no pueden ser luego editadas en el modo visual. Necesario incluir la siguiente línea en `configuration.yaml`.

  ```yaml
  automation: !include automations.yaml
  ```

- ***Scripts***. Del mismo modo, los [*scripts*](https://www.home-assistant.io/integrations/script/) ya son guardados por defecto en el archivo `scripts.yaml` en la carpeta raíz de configuración, y puede ser editado. Necesario incluir la siguiente línea en `configuration.yaml`.

  ```yaml
  script: !include scripts.yaml
  ```

- **Personalizaciones**. Mas de lo mismo. Archivo `customize.yaml`. Necesario incluir las siguientes líneas en `configuration.yaml`.

  ```yaml
  homeassistant:
    customize: !include customize.yaml
  ```

- **Areas**. **NO** es posible declararlas (y casi usarlas) en ningún archivo *YAML*.

- **Integraciones**. Depende. Algunas [integraciones](https://www.home-assistant.io/integrations/) están preparadas para incluirlas en `configuration.yaml`, otras en cambio, necesitan hacer uso de la sección *Configuración -> Integraciones* de *Home Assistant*, y no hay manera de moverlas a un archivo *YAML*. Revisa la documentación de cada integración para ver en qué casos puede moverse a *YAML* y en qué casos no. Por ejemplo, [*OpenWeatherMap*](https://www.home-assistant.io/integrations/openweathermap/) es una integración que puede pasarse a *YAML* fácilmente; [*ESPHome*](https://www.home-assistant.io/integrations/esphome/), no se puede mover por el momento. Mi recomendación es intentar configurar mediante *YAML* todas las integraciones que lo permitan.

- **Personas y Usuarios**.
  - Los usuarios **NO** pueden moverse a *YAML*, son del sistema y se pueden crear desde la sección *Configuración -> Usuarios* de *Home Assistant*. Son usados para identificar a cada individuo que puede usar el sistema.
  - [*Person*](https://www.home-assistant.io/integrations/person/) son entidades que asocian a cada usuario con diferentes rastreadores de dispositivos para indicar su posición. Estas entidades pueden declararse en *Configuración -> Personas* o en el archivo `configuration.yaml` igual que cualquier otra entidad.

    ```yaml
    person:
      - name: Dani
        id: dani
        user_id: 68fa5522b25e4f719fdfe20a398f508e
        device_trackers:
          []
    ```

- **Configuración General**. *Home Assistant* tiene una información general como el nombre de la casa o su posición *GPS*. Se puede configurar visualmente desde *Configuración -> General*. Pero también mediante *YAML* con las siguientes líneas dentro del nodo `homeassistant` en `configuration.yaml`.

  ```yaml
  homeassistant:
    name: Mi Casa
    latitude: 40.4167511
    longitude: -3.7036432
    elevation: 500
    unit_system: metric
    time_zone: Europe/Madrid
  ```

  > Como guinda del pastel, es muy recomendable mover los datos de latitud, longitud y elevación [al archivo `secrets.yaml`](/es/protege-tu-informacion-privada-con-secrets-yaml/).

  Puedes consultar posiciones *GPS* y elevaciones en webs como [CalcMaps](https://www.calcmaps.com/map-elevation/).

- ***Lovelace***. Pasar toda la configuración de interfaz gráfica a *YAML* no es complicado, pero una vez movida, todas las modificaciones de interfaz tendrán que realizarse siempre en *YAML*. Por ello, vamos a dejar para otro artículo toda la parte de *Lovelace*, así se podrá desarrollar ampliamente los diferentes modos de edición de interfaz y sus ventajas y desventajas.

## Conclusión

Al mover todas las opciones a *YAML* se obtiene una mejor organización de todas las configuraciones de nuestro sistema, así como una facilidad para realizar copias de seguridad, y hablando de seguridad. Ahora se puede usar el archivo `secrets.yaml` y tener los datos sensibles localizados en un solo archivo, permitiendo compartir nuestra configuración sin preocupaciones.

Solo faltaría mover la parte de *Lovelace*, que se verá en otro artículo.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
