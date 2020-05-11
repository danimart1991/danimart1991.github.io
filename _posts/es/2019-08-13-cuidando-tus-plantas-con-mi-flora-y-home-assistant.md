---
title: "Cuidando tus plantas con Mi Flora y Home Assistant"
header:
  image: /assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/header.jpg
tags: homeassistant hassio domotica
lang: es
ref: 16
permalink: /es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/
---

No soy una persona de plantas. La mayoría de las que he tenido caen en desgracia y acaban subiendo al cielo de las plantas. No es por no dedicarlas tiempo, simplemente no soy buen cuidador. Pero vengo a contaros mi secreto para que la orquídea de mi salón luzca y resalte entre el resto de flores mustias.

## *Xiaomi Mi Flora*

![Xiaomi Mi Flora](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image01.jpg)

La marca **Xiaomi** ofrece un sensor *Bluetooth Low Energy* para controlar varios estados de la tierra llamado ***Mi Flora*** (también llamado *Mi Plant*).

Este sensor lleva una pila de botón que dura varios meses y periódicamente envía los siguientes datos al dispositivo *Bluetooth* enlazado:

- Humedad (%)
- Temperatura (ºC)
- Conductividad (µS/cm)
- Luminosidad (Lux)
- Batería (%)

Gracias a todos estos datos, serás capaz de determinar si la planta está sana o necesita algún cuidado.

![Mi Home](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image02.jpg)

*Xiaomi* tiene una aplicación llamada **Mi Home** (para [Android](https://play.google.com/store/apps/details?id=com.xiaomi.smarthome) e [iOS](https://apps.apple.com/es/app/mi-home-xiaomi-smarthome/id957323480)) que ofrece un vínculo con el dispositivo **Mi Flora** y una base de datos enorme de plantas para así realizar la comparación con el estado actual de tu planta y determinar "su nivel de felicidad". Pese a ser una aplicación muy completa, ¿por qué no integrar todo en [*Home Assistant*](https://www.home-assistant.io/)?

## Integrando *Mi Flora* en *Home Assistant*

En el momento de escribir este artículo, *Home Assistant* no posee un asistente de integración para *Mi Flora*, por lo que debes configurar a mano la entidad en el archivo `configuration.yaml` como ya [se vió en ocasiones anteriores](/es/configura-home-assistant-editando-sus-archivos/). Antes de hacerlo, necesitas configurar el **adaptador *Bluetooth*** y conseguir la **dirección *MAC*** del dispositivo *Mi Flora*.

### Habilitando el *Backend Bluetooth*

Lo primero que debemos hacer en nuestro servidor Home Assistant es habilitar el soporte Bluetooth. Por supuesto, necesitas que tu dispositivo tenga Bluetooth. En mi caso, he puesto [este adaptador USB Bluetooth](https://amzn.to/2MBkb7v) con muy buenos resultados.

![Raspberry Pi + USB Bluetooth](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image03.jpg)

En Hass.io existe soporte nativo para Bluetooth, por lo que no es necesario nada más. En otros sistemas e instalaciones existen diferentes [métodos de instalación y configuración](https://www.home-assistant.io/components/miflora/#install-a-bluetooth-backend).

### Buscando el dispositivo

Para obtener la dirección *MAC* del dispositivo. Usa el siguiente comando en un **Terminal SSH** (puedes intuir cual es el dispositivo fijándote en las entradas que contengan **Flower care** o **Flower mate**):

```bash
$ sudo hcitool lescan
LE Scan ...
C4:D3:8C:12:4C:57 Flower mate
[...]
```

Si el comando `hcitool` no funciona, prueba usando `bluetoothctl`:

```bash
$ bluetoothctl
[bluetooth]# scan on
[NEW] Controller <your Bluetooth adapter> [default]
[NEW] C4:D3:8C:12:4C:57 Flower mate
```

Si no dispones de opciones para ejecutar estos comandos, hay disponibles varias aplicaciones para *Android*, *Windows*... que realizarán el escáner y te darán la dirección *MAC* del dispositivo.

## Configuración

Cumplidos los requisitos, llega la hora de editar el archivo `configuration.yaml` y añadir el siguiente código:

```yaml
# Plants
sensor:
  - platform: miflora
    mac: C4:D3:8C:12:4C:57 # La dirección MAC recogida en el paso anterior
    name: Mi Flora 01
    force_update: false
    median: 1
    monitored_conditions:
      - moisture
      - light
      - temperature
      - conductivity
      - battery
plant:
  # Orchidaceae, Phalaenopsis
  orchid:
    sensors:
      moisture: sensor.mi_flora_01_moisture
      battery: sensor.mi_flora_01_battery
      temperature: sensor.mi_flora_01_temperature
      conductivity: sensor.mi_flora_01_conductivity
      brightness: sensor.mi_flora_01_light_intensity
    min_moisture: 15
    max_moisture: 65
    min_battery: 20
    min_conductivity: 350
    max_conductivity: 2000
    min_temperature: 15
    max_temperature: 32
    min_brightness: 2000
    max_brightness: 30000
    check_days: 3
```

> **Nota:** Si tienes problemas con "picos" en las gráficas, es recomendable poner los valores `median: 3` y `force_update: true`.

Con esta configuración se creará una entidad para el dispositivo *Mi Flora* y sus sensores, así como una entidad planta que nos servirá para tener rangos de valores mínimo y máximo para estos sensores.

Reinicia tu servidor *Home Assistant*.

> Para el caso del valor `min_brightness` se utiliza el valor de `check_days` para calcular si durante ese número de días la luminosidad máxima registrada ha sido menor que `min_brightness`.

## Rangos de valores

Se pueden ver valores en la entidad `plant`, que a simple vista pueden parecer calculados de algún modo o puestos de manera aleatoria para el ejemplo.

![Orquídea con Mi Flora](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image04.jpg)

Nada más lejos de la realidad. Esos valores son los correspondientes con la orquídea donde está usándose actualmente *Mi Flora* en mi casa. Cada planta, tiene sus propios valores para determinar si está sana.

Hay varias formas de obtener estos datos:

- Instalando la aplicación **Mi Home**, buscando la planta y apuntando los valores que se muestran.

  ![Mi Home Base de Datos de plantas](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image05.jpg)

- Descargar una base de datos de plantas, por ejemplo [desde este repositorio de @khronimo](https://github.com/khronimo/MiFloraDB), buscar la planta deseada y apuntar los valores.

Una vez obtenidos, solo has de cambiar los valores por los de tu planta. Recuerda reiniciar después de realizar los cambios.

## Mostrando los resultados en *Lovelace*

Una vez configurado, hay varios métodos para mostrar una tarjeta con la entidad planta en la pantalla principal (**Resumen**) de *Home Assistant* dependiendo de cómo tengas configurado Lovelace:

- En **modo automático**, no debes hacer nada más que ir a la sección **Resumen**

  ![Tarjeta Plant Status Lovelace](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image06.jpg)

- En **modo storage**, añade una nueva tarjeta de tipo *Plant Status* y selecciona la entidad *plant* recién creada.

  ![Edición Tarjeta Plant Status Lovelace](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image07.jpg)

- En **modo YAML**, añade el siguiente código a `ui-lovelace.yaml` dentro del nodo `cards`:

  ```yaml
  cards:
    - type: plant-status
      name: Orquídea
      entity: plant.orchid
  ```

De cualquiera de las formas obtendrás el mismo resultado, una tarjeta que indica los valores actuales de los sensores, así como botones en cada uno para ver una gráfica de valores. En caso de que la planta tenga algún problema, el valor de la tarjeta se mostrará en color rojo para avisar de sus necesidades.

![Gráfica Temperatura](/assets/posts/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/image08.jpg)

## Conclusión

Con este sencillo tutorial has visto como poder integrar *Mi Flora* en *Home Assistant* y poder ver todos los datos que recoge en la pantalla *Resumen*. Espero que como a mí, este dispositivo te sirva para mantener tus plantas sanas y bonitas.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
