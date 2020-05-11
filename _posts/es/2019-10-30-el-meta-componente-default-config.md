---
title: "El meta-componente Default Config"
header:
  image: /assets/posts/es/el-meta-componente-default-config/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 23
permalink: /es/el-meta-componente-default-config/
---

Revisando y limpiando [el fichero `configuration.yaml` que en otras ocasiones hemos tenido que editar](/es/configura-home-assistant-editando-sus-archivos/). He visto el componente `default_config`. Un componente que viene por defecto activado y que hasta el momento no había hecho mucho caso.

El componente [**Default Config**](https://www.home-assistant.io/integrations/default_config/), es un meta-componente, esto quiere decir que al usarlo, configura por defecto otros muchos componentes de [**Home Assistant**](https://www.home-assistant.io/). El eliminarlo provoca que estos componentes dejen de funcionar y aquellos que queramos usar, tengamos que especificarlos uno a uno. ¿Por qué querríamos hacer esto? Para mejorar el rendimiento de nuestro servidor quitando componentes y funcionalidad que no estemos utilizando.

Vamos a analizar los componentes que incluye `default_config` e ir incluyendo en nuestro archivo `configuration.yaml` según necesitemos.

- **`automation`**: Habilita las [Automatizaciones](https://www.home-assistant.io/integrations/automation/). En la mayoría de casos, esta integración viene sustituida por la línea `automation: !include automations.yaml`, que agrupará todas las automatizaciones en el archivo `automations.yaml`. Si no vamos a usar automatizaciones en *Home Assistant*, todo este código puede ser borrado.
- **`cloud`**: Habilita [*Home Assistant Cloud*](https://www.home-assistant.io/integrations/cloud/) que permite acceso remoto a *Home Assistant*, guardado en la nube, o integración de servicios como *Amazon Alexa* o *Google Assistant* pagando una suscripción.
- **`config`**: Habilita la sección [Configuración](https://www.home-assistant.io/integrations/config/) de la parte visual (*frontend*) de *Home Assistant*. Desde ella podemos configurar de manera visual integraciones, usuarios, áreas, automatizaciones...
- **`frontend`**: Habilita el [entorno visual de *Home Assistant*](https://www.home-assistant.io/integrations/frontend/). Puede ser deshabilitado si se desea usar otros entornos visuales (*dashboard*). Esta integración incluye además varios parámetros interesantes como carga de código *HTML*, *CSS* o *JavaScript*, o poder añadir diferentes temas para cambiar los colores y el estilo.
  
  ```yaml
  frontend:
    themes:
      feliz:
        primary-color: pink
      triste:
        primary-color: blue
  ```

  Una vez definidos, se pueden seleccionar desde la configuración de usuario. La propia comunidad tiene [un gran listado de temas para usar](https://community.home-assistant.io/c/projects/themes).
- **`history`**: Habilita el [registro histórico](https://www.home-assistant.io/integrations/history/). Basándose en la configuración del componente `recorder` guarda un registro de todos los componentes y entidades que no estén ocultos (`hidden`). Permite además configurar entidades para ser excluidas o incluidas en el histórico. Todo el registro puede ser visualizado desde la pestaña **Historial**.
- **`logbook`**: Habilita el [mostrar el registro histórico](https://www.home-assistant.io/integrations/logbook/) (solo mostrarlo), en modo cronológico inverso, es decir, un listado de los cambios que han ido ocurriendo en *Home Assistant* a lo largo del tiempo. De nuevo, pueden incluirse o excluirse entidades. Puede ser visualizado desde la pestaña **Registro**.
- **`map`**: Habilita el [mapa](https://www.home-assistant.io/integrations/map/) que muestra los dispositivos rastreados (*tracked devices*) por *Home Assistant*. Puede ser visualizado desde la pestaña **Mapa**.
- **`mobile_app`**: Habilita la integración de [aplicaciones móviles](https://www.home-assistant.io/integrations/mobile_app/) con *Home Assistant*. Si no se van a usar aplicaciones externas que integran *Home Assistant*, se puede deshabilitar o borrar esta integración.
- **`person`**: Habilita la integración de [personas](https://www.home-assistant.io/integrations/person/) con sus entidades `device_tracker` usando *YAML*. Es decir, se pueden configurar personas con sus entidades de rastreo mediante el editor visual, o utilizar el fichero `configuration.yaml`.

  ```yaml
  person:
    - name: Dani
      id: dani1234
      user_id: 12345678912345678912345678912345
      device_trackers:
        - device_tracker.dani_phone
  ```

- **`script`**: Habilita el uso de [*Scripts*](https://www.home-assistant.io/integrations/script/). Igual que pasaba con las Automatizaciones, lo normal es usar la línea `script: !include scripts.yaml` y definir los *scripts* mediante *YAML* en el propio fichero o de manera visual mediante la sección **Configuración**.
- **`ssdp`**: Habilita el [*SSDP* (*Simple Service Discovery Protocol*)](https://www.home-assistant.io/integrations/ssdp/). Esta integración busca en la red dispositivos y servicios para añadirlos automáticamente a la sección **Integraciones** y que no tengamos que configurarlos manualmente.
- **`sun`**: Habilita la entidad [*Sun*](https://www.home-assistant.io/integrations/sun/) que simula la posición del sol basándose en la posición actual. Automáticamente obtiene la configuración de los datos indicados en la **Configuración General** de nuestro servidor. Es muy recomendable indicar la elevación para que los datos sean precisos.
- **`system_health`**: Habilita la *API* que ofrece la información de diferentes [diagnosticos del sistema](https://www.home-assistant.io/integrations/system_health/) y sus componentes. Puede ser consultada desde la sección **Herramientas para Desarrolladores**, pestaña **Info**.
- **`updater`**: Habilita el sensor que revisa diariamente si hay alguna [actualización del sistema disponible](https://www.home-assistant.io/integrations/updater/). Por defecto recolecta ciertos datos (ninguno sensible) para ser enviados a los servidores de *Home Assistant* para mejorar el servicio.
  > Aunque se indica que no tiene mucho sentido utilizar este sensor en [*Hass.io*](https://www.home-assistant.io/hassio/) pues ya posee un sistema de aviso de actualizaciones, es necesario si se quiere mostrar un sensor que nos avise visualmente.
- **`zeroconf`**: Habilita el componente [*Zeroconf*/*Avahi*/*Bonjour*](https://www.home-assistant.io/integrations/zeroconf/) que busca en la red servicios y dispositivos relacionados con *Zeroconf* y los añade automáticamente a la sección **Integraciones**.

Por último, recuerda borrar y modificar las entidades que ya no uses. Por ejemplo, si incluyes la entidad `person` de manera manual en el archivo `configuration.yaml`, has de acordarte de borrar la entidad antigua en la sección **Configuración -> Personas**, y en la sección **Configuración -> Registro de Entidades** y si lo has llamado con el mismo `id`, modifica la entidad nueva para que tenga el `id` que desees.

## Conclusión

Una vez añadidas las integraciones que nos interesan y con cuidado de borrar solo aquellas que estamos seguros no vamos a usar; hemos conseguido mejorar el rendimiento de nuestro servidor *Home Assistant* así como conocer un poco más la plataforma.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
