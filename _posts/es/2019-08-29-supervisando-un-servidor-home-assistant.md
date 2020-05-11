---
title: "Supervisando un servidor Home Assistant"
header:
  image: /assets/posts/es/supervisando-un-servidor-home-assistant/header.jpg
tags: homeassistant hassio domotica
lang: es
ref: 18
permalink: /es/supervisando-un-servidor-home-assistant/
---

Según va creciendo nuestra casa inteligente en número de dispositivos y complejidad, pero también en utilidad, debemos proteger [el servidor ***Home Assistant***](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/) con el fin de evitar problemas futuros o al menos detectarlos a tiempo.

[*Home Assistant*](https://www.home-assistant.io/) ofrece varios componentes de [supervisión de sistemas](https://www.home-assistant.io/components/#system-monitor). Para este artículo, vamos a utilizar los componentes ***System Monitor*** y [***Speedtest.net***](https://www.speedtest.net/) para obtener valores del sistema y datos de la red respectivamente.

![Componentes System Monitor y Speedtest.net](/assets/posts/es/supervisando-un-servidor-home-assistant/image01.jpg)

## Configurando los componentes

De igual modo que en [otras ocasiones](/es/configura-home-assistant-editando-sus-archivos/), vamos a editar el archivo `configuration.yaml` para incluir los componentes [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/) y [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/). Abre el archivo e incluye las siguientes líneas de código:

```yaml
speedtestdotnet:
  scan_interval:
    minutes: 30
  monitored_conditions:
    - ping
    - download
    - upload

sensor:
  - platform: systemmonitor
    resources:
      - type: disk_use_percent
        arg: /
      - type: memory_use_percent
      - type: processor_use
      - type: last_boot
```

En el caso de tener el nodo sensor con alguna otra plataforma (por ejemplo [`miflora`](https://www.home-assistant.io/components/miflora/)), simplemente agrega `systemmonitor` a la lista:

```yaml
...
sensor:
  - platform: systemmonitor
    ...
  - platform: miflora
    ...
...
```

Los componentes [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/) y [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/) tienen otros parámetros muy interesantes, pero por el momento, voy a poner los que considero más interesantes o son obligatorios para que funcione la configuración. Revisa la documentación de cada uno para descubrir todas las opciones disponibles.

> El componente [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/) consume un gran ancho de *CPU* (que se podrá analizar desde el componente [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/)), si notas cortes o problemas de rendimiento, aumenta el parámetro `scan_interval` para realizar el análisis de red cada varias horas.
<br/><br/>
![Tarjeta Speedtest.net](/assets/posts/es/supervisando-un-servidor-home-assistant/image02.jpg)

**Reinicia el servidor** desde la configuración.

## Interfaz

Para mostrar los datos en el [*Dashboard* de *Lovelace*](/es/conociendo-home-assistant/#lovelace), vamos a utilizar dos tarjetas especiales. Estas tarjetas no pueden configurarse de modo visual, así que editaremos su código.

Para `speedtestdotnet` añade una tarjeta de tipo ***History-Graph***:

```yaml
entities:
  - sensor.speedtest_download
  - sensor.speedtest_ping
  - sensor.speedtest_upload
type: history-graph
```

![Tarjeta Speedtest.net](/assets/posts/es/supervisando-un-servidor-home-assistant/image03.jpg)

Para el sensor `systemmonitor`, crea una tarjeta de tipo ***Vertical-Stack***:

```yaml
cards:
  - cards:
      - type: gauge
        entity: sensor.processor_use
      - type: gauge
        entity: sensor.disk_use_percent
      - type: gauge
        entity: sensor.memory_use_percent
    type: horizontal-stack
  - entities:
      - sensor.last_boot
    type: entities
type: vertical-stack
```

![Tarjeta System Monitor](/assets/posts/es/supervisando-un-servidor-home-assistant/image04.jpg)

## Conclusión

Con esta información, y con otra que pueda interesarte (mirar documentación de los componentes), podrás supervisar tu servidor y predecir problemas futuros antes de que ocurran de una manera sencilla y visual.

Por ejemplo, ver si el espacio disponible en el servidor está acabándose, o si el operador de Internet está ofreciendo menos velocidad de la contratada.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
