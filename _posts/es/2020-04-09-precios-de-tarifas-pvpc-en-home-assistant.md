---
title: "Precios de tarifas PVPC en Home Assistant"
header:
  image: /assets/posts/es/precios-de-tarifas-pvpc-en-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 32
permalink: /es/precios-de-tarifas-pvpc-en-home-assistant/
---

A fecha de publicación de este artículo hace apenas un mes que he cambiado mi tarifa de luz a [*PVPC* (Precio Voluntario al Pequeño Consumidor)](https://www.esios.ree.es/es/pvpc), que es la tarifa de luz por horas regulada por el Gobierno.

Esta tarifa se caracteriza por ofrecer un precio de la luz diferente para cada hora del día. Así, el consumidor tendrá 24 precios de la luz para cada jornada.

Por si esto fuera poco, dispone para su contratación de varios peajes, esto es, periodos en los que los precios cambian drásticamente durante ciertas horas del día. Por ello es importante saber el precio de la luz en cada hora para poder sacar el máximo beneficio a las tarifas *PVPC*.

<iframe src="https://www.esios.ree.es/en/embed/active-energy-invoicing-price-pvpc" width="100%" height="608"></iframe>

## Configuración

Por suerte para nosotros, [Eugenio Panadero](https://github.com/azogue) ha creado una integración para darnos toda la información y poderla integrar de manera muy sencilla en [*Home Assistant*](https://www.home-assistant.io/).

La [integración](https://www.home-assistant.io/integrations/pvpc_hourly_pricing/) se encuentra disponible de manera nativa a partir de la versión [*0.108* de *Home Assistant*](https://www.home-assistant.io/blog/2020/04/08/release-108/). En caso de tener una versión menor, está disponible un [*custom component* para su instalación](https://github.com/azogue/hassio_config/tree/master/config/custom_components/pvpc_hourly_pricing).

### Configuración visual

La manera más sencilla de configurar la integración es desde el menú *Configuración -> Integraciones* de *Home Assistant*. Añadimos una nueva integración y buscamos `pvpc`.

![Integración visual](/assets/posts/es/precios-de-tarifas-pvpc-en-home-assistant/pvpc-integracion-visual.jpg)

A continuación, indica un nombre y el tipo de tarifa contratada. Guarda.

![Selección de tarifa](/assets/posts/es/precios-de-tarifas-pvpc-en-home-assistant/pvpc-seleccion-tarifa.jpg)

Tendrás disponible un sensor con el nombre que has indicado en el paso anterior con toda la información.

![Ejemplo de integración](/assets/posts/es/precios-de-tarifas-pvpc-en-home-assistant/ejemplo-integracion-pvpc.jpg)

> Puedes configurar hasta 3 sensores, uno por cada tarifa disponible.

### Configuración avanzada

Como suele ser habitual en las integraciones de *Home Assistant*, también se puede configurar modificando el archivo `configuration.yaml` y añadiendo las siguientes líneas de código:

```yaml
pvpc_hourly_pricing:
  - name: "PVPC 2.0 DHA"
    tariff: discrimination
```

El campo `tariff` admite `normal`, `discrimination` y `electric_car` según tengamos contratado 1, 2 o 3 periodos respectivamente.

## Dashboard

Aunque la integración trae bastantes datos referentes a *PVPC*, a la hora de mostrarlos en un *Dashboard* con [*Lovelace*](https://www.home-assistant.io/lovelace/) vemos que no hay ninguna tarjeta que permita mostrar de manera visual el precio diario o del día siguiente de manera completa.

Es por ello que he creado una *Custom Card* para poder ver más o menos en nuestros *Dashboard* el mismo gráfico que tenemos disponible en la web del [Sistema de Información del Operador del Sistema](https://www.esios.ree.es/es/pvpc).

[**En este otro artículo se puede ver como instalar y configurar la tarjeta.**](/es/instalando-plugins-de-lovelace/)

![Ejemplo tarjeta](/assets/posts/es/precios-de-tarifas-pvpc-en-home-assistant/ejemplo-tarjeta.jpg)

## Conclusión

De manera sencilla se ha configurado la integración *PVPC* (Precio Voluntario al Pequeño Consumidor) y una tarjeta personalizada donde ver todos los datos disponibles de una manera muy visual.

Ahora solo queda intentar usar los aparatos que más consuman en las horas del día más baratas y disfrutar de los beneficios de este tipo de tarifas.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
