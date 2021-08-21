---
title: "Medidor de Consumo No Invasivo con PZEM y ESPHome"
header:
  image: /assets/posts/es/medidor-consumo-no-invasivo-pzem-esphome/header.png
categories:
  - Tutorial
  - ESPHome
  - Domótica
tags:
  - ESPHome
  - Medidor
  - Energia
  - Consumo
  - Tutorial
  - Pzem
lang: es
ref: 43
permalink: /es/medidor-consumo-no-invasivo-pzem-esphome/
excerpt: Con el precio de la electricidad en máximos históricos, y la previsión a su continuo incremento, creemos que es necesario tener una forma de medir el consumo de electricidad y las partes de la casa con mayor gasto energético con el fin de poder optimizar su uso, y, de esta manera reducir nuestra factura.
---

> Este post ha sido patrocinado por las donaciones de los usuarios que la visitan. Mención especial a **@cantavro** por su aportación para poder costear los materiales. Puedes apoyarme a través de [_Paypal_](https://www.paypal.me/danimart1991), [_GitHub_](https://github.com/sponsors/danimart1991) o simplemente desactivando el bloqueador de publicidad.

Con **el precio de la electricidad en máximos históricos**, y la previsión a su continuo incremento, creemos que es necesario tener una forma de **medir el consumo de electricidad y las partes de la casa con mayor gasto energético** con el fin de poder optimizar su uso, y, de esta manera reducir nuestra factura. Una manera sencilla de conseguir este objetivo es mediante la instalación de medidores de consumo por dispositivo y/o a nivel global de todo el hogar. En este artículo vamos a centrarnos en **colocar un medidor de consumo en el cuadro eléctrico para medir el consumo de toda nuestra casa**.

Existen en el mercado multitud de dispositivos que cumplen este cometido. Pero desde este blog hemos preferido fabricarnos uno propio por muy poco dinero y que además no es invasivo, es decir, no requiere que la línea con la corriente a medir pase por el dispositivo, por tanto, su montaje y uso es más seguro.

![Tipos de Medidor de Corriente](/assets/posts/es/medidor-consumo-no-invasivo-pzem-esphome/tipos_medidor_corriente.png)

> **AVISO:** Este tutorial es avanzado y requiere tocar el cuadro eléctrico de la casa, eso implica ciertos conocimientos de electricidad y electrónica, así como asegurar el corte total de la corriente antes de su manipulación.

## Materiales

- **Un módulo _ESP8266_ o _ESP32_** que usaremos para obtener los datos del medidor y transmitirlos por _Wi-Fi_ a nuestro servidor. Puedes encontrar distintos modelos en [_Amazon_](https://amzn.to/3mnSPn9).
- **Medidor _PZEM-004T V3_** que puedes comprar en [_Amazon_](https://amzn.to/3ycMoFz). También vale la versión 1, pero ofrece menos datos.
- **Una pinza / anillo de corriente _CT_**. Normalmente incluido con el medidor, ha de aguantar como mínimo la intensidad límite que aguante la línea que vamos a medir. Puedes elegir si usar una pinza, que, aunque no requiere la desconexión del cable que queremos medir, nos da unos datos menos precisos, o un anillo, que tiene más precisión, pero requiere que previamente pasemos el cable, con lo que habrá que desmontarlo.
- **Cables _dupont_** para conectar ambos dispositivos. Aunque lo ideal es realizar un circuito puedes usar cables _dupont_ y una placa de desarrollo para realizar el tutorial.
- **Un transformador de _220V_ a _5V_** como un cargador móvil o un transformador más adecuado que podemos encontrar de nuevo en [_Amazon_](https://amzn.to/3sEnNZt).
- **Cables Azul y Marrón** para enchufar el medidor _PZEM-004T_ a la corriente.

## Software

Primero vamos a **meter el _software_ generado con _ESPHome_ al módulo _ESP_**. Si no estás familiarizado con _ESPHome_, te recomiendo [leer este artículo](https://www.danielmartingonzalez.com/es/crea-tus-propios-dispositivos-inteligentes-con-esphome/) y pasarte por su documentación [aquí](https://esphome.io/).

El siguiente código es solo una plantilla que se puede adaptar a las necesidades de cada uno:

```yaml
# Recuerda cambiar la plataforma y placa según corresponda a tu módulo ESP.
# https://esphome.io/components/esphome.html
# ESP8266: https://esphome.io/devices/nodemcu_esp8266.html
# ESP32: https://esphome.io/devices/nodemcu_esp32.html
esphome:
  name: "medidor-consumo-pzem004t"
  platform: ESP32
  board: nodemcu-32s

# Configuración Wi-Fi al que se conectará el módulo
# https://esphome.io/components/wifi.html
wifi:
  ssid: <NOMBRE_RED_WIFI>
  password: <CONTRASEÑA_RED_WIFI>
  manual_ip:
    static_ip: <IP_FIJA_DEL_MODULO>
    subnet: <SUBRED_DE_RED>
    gateway: <PUERTA_DE_ENLACE_DE_RED>

# API para que Home Assistant pueda conectarse al módulo
# https://esphome.io/components/api.html
api:

# Servidor Web para poder consultar información por web
# https://esphome.io/components/web_server.html
web_server:

# Componente OTA para poder actualizar el módulo sin necesidad de cables
# https://esphome.io/components/ota.html
ota:

# Logger para registrar los mensajes que manda el dispositivo y poder depurar
# https://esphome.io/components/logger.html
logger:

# Indicamos los pines donde hemos conectado TX y RX del dispositivo, teniendo en cuenta que siempre han de ser invertidos TX->RX, RX->TX
uart:
  rx_pin: GPIO16
  tx_pin: GPIO17
  baud_rate: 9600
  # stop_bits solo es necesario si así lo indica el log mientras probamos el circuito
  #stop_bits: 2

sensor:
  # Usaremos pzemac o pzem004t dependiendo de si estamos usando un PZEM-004T V3 o V1
  - platform: pzemac
    current:
      name: "Current"
    voltage:
      name: "Voltage"
    energy:
      name: "Energy"
    power:
      name: "Power"
    frequency:
      name: "Frequency"
    power_factor:
      name: "Power Factor"
    update_interval: 30s

# Indicamos el pin del LED de la placa para que parpadee según su estado
# https://esphome.io/components/status_led.html
status_led:
  pin: GPIO2
```

Generamos el binario compilado del archivo `.yaml` y lo metemos en el módulo _ESP_. Como siempre, se puede realizar por _USB_ o si ya lo tenemos configurado previamente por _OTA_. Una vez listo pasamos al circuito.

## Conexiones

Vamos a seguir un esquema bastante sencillo que puede ser adaptado a distintas necesidades y montajes. Por ahora, y para realizar las pruebas...

![Esquema Conexiones PZEM004-T a NodeMCU ESP32S](/assets/posts/es/medidor-consumo-no-invasivo-pzem-esphome/esquema.png)

| NodeMCU ESP32S | PZEM004-T V3    | Transformador | Anillo |
| -------------- | --------------- | ------------- | ------ |
| 5V             | 5V              | Rojo/+/VCC/5V |        |
| GND            | GND             | Negro/-/GND   |        |
| GPIO16         | TX              |               |        |
| GPIO17         | RX              |               |        |
|                | CT (Cualquiera) |               | Negro  |
|                | CT (Cualquiera) |               | Rojo   |
|                | Fase            | Fase          |        |
|                | Neutro          | Neutro        |        |

**Fase y Neutro irán conectados a corriente directa de la casa.**

Por el interior del anillo tendremos que pasar el cable de Fase (Negro) sobre el que queramos realizar las mediciones, pudiendo ser el mismo, o no, que el que hemos usado para dar corriente al circuito.

> **Nota:** Algunos módulos _ESP_ permiten la salida de corriente _5V_ por el pin, otros no. Si vas a alimentar el módulo _ESP_ con un cable _USB_ y desde sus pines _5V/VCC_ y _GND_ alimentar el módulo _PZEM-004T_ **comprueba que estos pines ofrezcan la corriente deseada**.

La idea es realizar pruebas sobre este esquema y luego realizar un montaje e instalación más adecuadas y seguras, ya que recordemos que va a ir dentro del cuadro eléctrico.

> Dado que realmente el módulo _ESP_ funciona a _3,3V_ y si se desea alimentar el módulo _PZEM-004T_ con este voltaje, se recomienda colocar una resistencia de _1K_ entre _5V_ y _RX_ del propio _PZEM-004T_. La [_Web_ de _Tasmota_](https://tasmota.github.io/docs/PZEM-0XX/) tiene mucha información al respecto.

## Probando

Una vez configurado el _software_ y conectado el circuito, podemos enchufar el transformador, esperamos unos segundos y ya podemos **acceder al Servidor _Web_** escribiendo la _IP_ del dispositivo en cualquier navegador _Web_.

![ESPHome Web Server mostrando datos de PZEM004-T](/assets/posts/es/medidor-consumo-no-invasivo-pzem-esphome/esphome-pzem-webserver.png)

Si hemos realizado bien las conexiones y la línea que estamos midiendo está realizando consumo, veremos cómo los valores mostrados van cambiando.

**¡Ya estaría listo nuestro medidor de consumo!**

## Conclusión

Mejorando el circuito y protegíendolo podremos montar el medidor de consumo que acabamos de construir en nuestro cuadro eléctrico y controlar el consumo y demás parámetros que aporten datos sobre cómo se comporta nuestra instalación eléctrica. Con ello optimizar gastos y detectar posibles consumos fantasma.

Si tenemos espacio en el cuadro, podemos comprar cajas con soporte para carril _din_ ([en _Amazon_ hay varios modelos](https://amzn.to/2XPcuCh)) y colocar dentro los componentes.

Este y otros artículos complementan la documentación del [**repositorio de _GitHub_**](https://github.com/danimart1991/esphome-config) donde se encuentra disponible toda la configuración de mis dispositivos _ESP8266_/_ESP32_ con _ESPHome_.

## Ver también

- [Domology - Medidor de consumo no invasivo, con ESP, y tasmota](https://domology.es/medidor-de-consumo-no-invasivo-con-esp-y-tasmota/)
- [ESPHome - Peacefair PZEM-004T Energy Monitor](https://esphome.io/components/sensor/pzem004t.html)
- [ESPHome - Peacefair PZEM-004T V3 Energy Monitor](https://esphome.io/components/sensor/pzemac.html)
- [Tasmota - PZEM-0xx power monitor](https://tasmota.github.io/docs/PZEM-0XX/)
