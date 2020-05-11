---
title: "Archivos YAML por paquetes en Home Assistant"
header:
  image: /assets/posts/es/archivos-yaml-por-paquetes-en-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 30
permalink: /es/archivos-yaml-por-paquetes-en-home-assistant/
---

Según pasa el tiempo y se van agregando cada vez más integraciones a un servidor de [***Home Assistant***](https://www.home-assistant.io/) se observa como los archivos de configuración son cada vez más complicados y grandes, y aunque se intente [dividir en diferentes archivos según el tipo de integración](https://www.home-assistant.io/docs/configuration/splitting_configuration/), cada dispositivo nuevo modifica varios archivos y cualquier actualización puede suponer un verdadero quebradero de cabeza. Es necesario dar una vuelta de tuerca a la organización de los archivos de configuración *YAML* de la mano de los [***Packages***](https://www.home-assistant.io/docs/configuration/packages/) (paquetes).

## Packages

La idea detrás de los *Packages* es agrupar las integraciones de "cosas" que estén relacionadas entre sí, pudiendo en este caso repetir tipos de integración entre los diferentes paquetes (al contrario que pasa en otras alternativas). Además, se puede aprovechar todo lo ya aprendido, como por ejemplo la definición `!include`.

De una manera sencilla, se puede distribuir las integraciones relativas a un mismo tema en uno o varios archivos y mejorar el mantenimiento y lectura de estas integraciones. Incluso poder compartirlas con otras personas sin tener que hacer muchos cambios.

Se puede incluir además personalizaciones, scripts, automatizaciones...

A continuación, un ejemplo dividiendo diferentes integraciones por habitaciones usando *packages*:

```yaml
######################
# configuration.yaml #
######################

homeassistant:
  ...
  packages:
    - habitacion1: !include habitacion_1.yaml
    - habitacion2: !include habitacion_2.yaml
```

```yaml
#####################
# habitacion_1.yaml #
#####################

binary_sensor:
  - platform: template
    sensors:
      temp_hab1:
        ...
light:
  - platform: hue
    ...
climate:
  - platform: generic_thermostat
    name: "Termostato Habitación 1"
    ...
```

```yaml
#####################
# habitacion_2.yaml #
#####################

binary_sensor:
  - platform: template
    sensors:
      temp_hab2:
        ...
light:
  - platform: hue
    ...
automation:
  - id: encender_luz
    ...
```

> Cabe destacar que aunque *Packages* agrupa de manera parecida a la integración [*Group*](https://www.home-assistant.io/integrations/group/), su uso queda relegado a la organización de la configuración. *Home Assistant* solo usará el contenido que se encuentre dentro de cada *package*.

## Carpeta packages

![Carpeta Packages](/assets/posts/es/archivos-yaml-por-paquetes-en-home-assistant/carpeta-packages.jpg)

Aún se puede ir un paso más lejos y crear una carpeta en la raíz de nuestra configuración llamada *packages* donde guardar cada uno de los archivos *YAML* que se van creando, y en lugar de declarar uno a uno en el archivo `configuration.yaml`, se puede escribir la siguiente línea:

```yaml
homeassistant:
  ...
  packages: !include_dir_named packages
...
```

Puede verse un ejemplo de configuración siguiendo este patrón en el [repositorio con la configuración de mi servidor](https://github.com/danimart1991/home-assistant-config).

Ahora solo queda crear cada uno de los archivos e incluir todas las integraciones, automatizaciones, scripts... que se quieran agrupar.

## Conclusión

Cuando la cantidad de líneas de configuración va creciendo, llega el momento de reorganizar todo un poco. ***Packages*** ofrece una alternativa mucho más sostenible y escalable.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
