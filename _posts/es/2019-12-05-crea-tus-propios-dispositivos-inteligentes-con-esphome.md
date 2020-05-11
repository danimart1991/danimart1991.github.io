---
title: "Crea tus propios dispositivos inteligentes con ESPHome"
header:
  image: /assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/header.jpg
categories:
  - IoT
  - Home Assistant
  - ESPHome
lang: es
ref: 25
permalink: /es/crea-tus-propios-dispositivos-inteligentes-con-esphome/
---

[***ESPHome***](https://esphome.io/) es un [*framework*](https://es.wikipedia.org/wiki/Framework) (modulo *software*) para controlar los módulos [***ESP8266/ESP32***](/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/) de una manera muy sencilla pero potente y crear dispositivos inteligentes. De igual modo, permite modificar el sistema de otros dispositivos que utilicen estos módulos, como por ejemplo la gama [**Sonoff**](https://www.itead.cc/sonoff-wifi-wireless-switch.html), para hacerlos mucho más potentes y versátiles.

Existen otras alternativas a *ESPHome* como [*Espurna*](https://github.com/xoseperez/espurna) o [*Tasmota*](https://github.com/arendst/Tasmota), todas bastante parecidas. Al final el objetivo es poder configurar dispositivos propios y dispositivos comprados para evitar usar servidores y aplicaciones externas como [*Tuya Smart Life*](https://www.tuya.com/) o [*eWeLink*](https://sonoff.tech/ewelink) y añadir más funcionalidad.

> Para seguir este artículo, es recomendable tener conocimientos básicos de electrónica, así como informarse del dispositivo que se va a construir. Aun así, estaré encantado de responder cualquier duda, solo tienes que preguntar en los comentarios.

## Instalación

El funcionamiento de *ESPHome* es muy sencillo. Mediante unos archivos de configuración [*YAML*](https://yaml.org/), se configura el dispositivo y se añaden las características que así se desee, el compilador genera un archivo binario que se introducirá en el dispositivo y sustituirá el sistema anterior. Todo ello se realiza desde el propio *ESPHome*. Por ello, al contrario que en otras alternativas, es necesario realizar la instalación de este. Existen varios métodos:

- [Instalación del software en un sistema operativo *Linux* con un entorno *Python* compatible](https://esphome.io/guides/getting_started_command_line.html).
- [Instalación mediante contenedor *Docker*](https://esphome.io/guides/getting_started_command_line.html).
- [Instalación mediante *Add-On* de *Hass.io*](https://esphome.io/guides/getting_started_hassio.html).

Todas ellas disponen de una interfaz de consola e interfaz gráfica. En nuestro caso por comodidad, vamos a proceder a realizar la instalación del [***Add-On ESPHome para Hass.io***](https://github.com/esphome/hassio).

1. [Como ya se ha visto en otros artículos](/es/conociendo-home-assistant/#instalando-nuestro-primer-add-on), la instalación pasa por abrir nuestro entorno web de [*Home Assistant*](https://www.home-assistant.io/), ir a la sección *Hass.io -> Add-on Store*.

2. Desde este punto, y dado que es un *Add-on* externo, se debe incluir el repositorio de *ESPHome* en la lista de repositorios de *Add-ons*: `https://github.com/esphome/hassio`.

    ![Incluir repositorio ESPHome en Hass.io](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image01.jpg)

3. Una vez incluido el repositorio, busca e instala el *Add-on ESPHome*.

    ![Instalación Add-on ESPHome](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image02.jpg)

4. Opcionalmente marca la opción `Show in sidebar` para que se muestre la sección *ESPHome* en el menú izquierdo.

5. Si estás usando certificados *SSL*, indica los archivos de certificados y marcar a `true` el campo `ssl` en el cuadro de configuración.

    ```json
    {
      "ssl": true,
      "certfile": "fullchain.pem",
      "keyfile": "privkey.pem"
    }
    ```

6. Guarda e inicia el *Add-on*.

7. Abre la interfaz gráfica de *ESPHome* haciendo clic en `OPEN WEB UI`.

    ![Dashboard Add-on ESPHome](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image03.jpg)

## Montaje de un dispositivo de ejemplo

Aprovechando el sensor [*Xiaomi Mi Flora* que se configuró en otro artículo](/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/) se va a proceder a la creación de un primer dispositivo de ejemplo. En este caso, el sensor va a colocarse en otro punto de la casa, a demasiada distancia del servidor *Home Assistant* y de su rango *Bluetooth*, sin posibilidad de vincular y recibir datos.

La solución es configurar un dispositivo propio usando [ESP32 NodeMCU](https://amzn.to/2OYJaBh) (placa de desarrollo), colocado cerca de *Xiaomi Mi Flora*, que se vinculará por *Bluetooth* y mandará la información a *Home Assistant* usando *API* de *ESPHome* mediante *WiFi*.

![Esquema conexión ESP32, Mi Flora y Home Assistant](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image04.jpg)

Una vez aclarado el esquema, conecta el módulo *ESP32 NodeMCU* al servidor de *ESPHome* (en nuestro caso la *Raspberry Pi* donde tenemos el servidor de *Home Assistant*). Y reinicia el servidor *ESPHome* desde su página *Add-on* para que detecte la nueva conexión *USB*.

![Reinicio Add-on ESPHome](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image05.jpg)

A continuación, selecciona arriba a la derecha la entrada de dispositivo *USB* que has conectado y haz clic en botón **`+`** para crear una nueva configuración *YAML*.

![Selección dispositivo](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image06.jpg)

Indica un **nombre** y **contraseña** para tu dispositivo, el tipo de placa (en el caso del *NodeMCU ESP-32*, elige ***NodeMCU-32S***) y la **configuración de tu red *WiFi***.

![Selección dispositivo](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image07.jpg)

Haz clic en ***Submit***.

![Configuración final](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image08.jpg)

Aparecerá el nuevo dispositivo en el listado.

![Listado de dispositivos ESPHome](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image09.jpg)

El siguiente paso es completar el archivo *YAML* con la configuración deseada. La configuración básica y la configuración para dar soporte al dispositivo *Mi Flora* es como sigue:

```yaml
# Configuración básica
esphome:
  name: esp32_01
  platform: ESP32
  board: nodemcu-32s

# Configuración red WiFi
wifi:
  ssid: SSID_DE_RED_WIFI
  password: PASSWORD_RED_WIFI

# API con Home Assistant
api:

# Actualizaciones por OTA
ota:

# Log de información y errores
logger:

# Activación Bluetooth
esp32_ble_tracker:

# Sensor Mi Flora
sensor:
  - platform: xiaomi_hhccjcy01
    mac_address: XX:XX:XX:XX:XX # Dirección MAC de Mi Flora
    temperature:
      id: mi_flora_temperatura
    moisture:
      id: mi_flora_humedad
    illuminance:
      id: mi_flora_luminosidad
    conductivity:
      id: mi_flora_conductividad
    battery_level:
      id: mi_flora_bateria

# Estado del dispositivo (Sensor)
binary_sensor:
  - platform: status
    id: ${devicename}_estado

# Estado del dispositivo (LED)
status_led:
  pin: GPIO2
```

Una vez terminado, guarda los cambios con ***Save***, y asegurando tener seleccionado la entrada *USB* arriba a la derecha, haz clic en ***Upload***.

![Save y Upload](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image10.jpg)

La configuración será compilada y desplegada en el dispositivo. Una vez terminado y al tener activado el *log*, se podrán ver los datos que se reciben desde *Mi Flora*.

![Log sensor Mi Flora](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image11.jpg)

A partir de este momento se podrán realizar los despliegues usando la entrada ***OTA (Over-The-Air)*** sin necesidad de conectar el dispositivo al servidor de *ESPHome* físicamente.

## Integración en Home Assistant

El dispositivo que acabamos de crear, montar y configurar ya recibe datos de *Xiaomi Mi Flora* por *Bluetooth*, pero esos datos queremos mostrarlos en nuestro servidor *Home Assistant*.

En este caso, no existe una configuración manual editando el archivo `configuration.yaml`. La manera de incluir dispositivos que usan la ***API*** de *ESPHome* es mediante el panel de **Configuración -> Integraciones**.

Desde este punto, haz clic en el botón **`+`** de la esquina inferior derecha, y busca *ESPHome*. En el cuadro que se muestra, indica el nombre del *host*, que suele ser el nombre del dispositivo + `.local` (`esp32_01.local`), o la *IP* asignada por el *router*, y finalmente haz clic en **Enviar**.

![Configuración dispositivo ESPHome en Home Assistant](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image12.jpg)

Si así lo deseas puedes vincular el dispositivo a una habitación.

Se mostrará el dispositivo en el listado de dispositivos integrados en el servidor.

![Listado dispositivos integrados en Home Assistant](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image13.jpg)

Dependiendo de como estés usando la interfaz visual *Lovelace*, los sensores aparecerán automáticamente, o tendrás que incluirlos en tarjetas a tu gusto.

![Dashboard Home Assistant](/assets/posts/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/image14.jpg)

## Conclusión

De manera sencilla se ha creado un servidor para configuración y despliegue de *firmware* basado en *ESPHome* para dispositivos vendidos en tiendas o hechos por nosotros mismos. Y hemos configurado un dispositivo sencillo que es capaz de recibir información de otro dispositivo por *Bluetooth* y enviarlo a un servidor con *Home Assistant*. *ESPHome* es mucho más potente de lo visto en este artículo, en *Internet* podrás descubrir diseños que hacen otras personas y que puedes adaptar a tus necesidades y proyectos de manera sencilla.

Por mi parte, al momento de escribir este artículo ya tengo varios sensores de temperatura, humedad y presión repartidos por toda la casa. Uno de ellos vinculado como hemos visto con *Xiaomi Mi Flora*, y un dispositivo *Sonoff Basic* con el sistema *ESPHome* que lo hace funcionar en la red local y permite añadir funcionalidad que de otro modo no se podría conseguir. ¡Cualquier proyecto es posible!

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
