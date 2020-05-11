---
title: "Conociendo ESP8266 / NodeMCU, el módulo WIFI para IoT"
header:
  image: /assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/header.jpg
tags: [ Español, IoT, NodeMCU, ESP8266, Arduino, Putty ]
categories: [ IoT ]
lang: es
ref: 3
permalink: /es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/
---

Por suerte o desgracia el mundo del **IoT** se está convirtiendo en una maraña de dispositivos, servicios, software y sistemas operativos. Esto no tiene porque ser malo, de hecho es una gran noticia para los Makers que quieran aplicar soluciones artesanales a sus proyectos e ideas. Sin embargo, en cierto grado, puede llegar a ser un caos difícil de comprender, sobre todo para los que están empezando.

Hoy, después de un par de días curioseando, vengo a presentaros mi experiencia con el módulo **WIFI** por excelencia, **NodeMCU** / **ESP8266**.

## ESP8266 vs. NodeMCU

Antes de empezar, conviene diferenciar estos dos chips.

### ESP8266

![ESP-01](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/esp-01.jpg)

**ESP8266** es un módulo **WIFI** muy utilizado por la comunidad. Razones para ello, muchas. Desde facilidad de uso, tamaño, versatilidad,… Puede usarse principalmente como esclavo de una placa Arduino, o usarlo por cuenta propia con el firmware adecuado.

De hecho el **módulo ESP8266** incluye un pequeño procesador interno que podríamos programar para funcionar de modo autónomo y que incluso dispone de un par de puertos **GPIO** (*General Purpose Input Output*) para su uso como activador de algún otro componente IOT.

Si queréis conocer más, os animo a visitar [los foros](http://www.esp8266.com/) dedicados a esta joya.

### NodeMCU

![NodeMCU](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/nodemcu.jpg)

[NodeMCU](http://nodemcu.com) es una placa **Open Source** que facilita el uso del **ESP8266** a nivel de hardware y software. Incluye conexión **USB**, pines **IO** preparados para **Breadboard** y acceso total al chip **ESP8266**. Y lo mejor de todo, a un precio de derribo, apenas [**8€ en Amazon**](http://amzn.to/2lp3HPG). Si vais a comprar esta placa, buscad la última versión disponible. Os dejo [una estupenda guía](http://frightanic.com/iot/comparison-of-esp8266-nodemcu-development-boards/) que compara las distintas versiones disponibles.

Su tamaño y versatilidad lo hace perfecto para pequeños proyectos. Ya que puedes conectar una batería, *USB*, sensores… y meter todo en una pequeña cajita.

Se programa principalmente en **LUA**; se cargan scripts en **LUA** al micro para cargarlos al inicio, aunque también podemos interactuar con él mediante consola con comandos en tiempo real. Por otra parte, la placa está disponible en **Arduino IDE**, con lo que podemos trabajar como si de una placa de Arduino se tratara, incluyendo lenguaje de programación y conocimiento de la comunidad.

## Empezando con NodeMCU

Uno de los problemas principales que he tenido al comenzar con esta placa, es que no hay mucha ayuda referente a como comenzar, hay tutoriales básicos para hacer cosas pequeñas, pero no te dicen que hacer cuando llega la placa **NodeMCU** a tu casa.

Lo primero que tienes que conocer son los pines que trae esta placa.

![NodeMCU Pinout](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/nodemcu-pinmap.png)

No son muchos, pero si muy útiles. Como vemos tiene varios pines **IO** y formas de dar corriente al dispositivo. La más sencilla es usar un cable **USB-microUSB**. Aunque pueden usarse transformadores y baterías para hacer el dispositivo más autónomo todavía.

Necesitamos además **descargar e instalar** lo siguiente:

- [Drivers](https://github.com/nodemcu/nodemcu-devkit/tree/master/Drivers)
- [Putty](http://www.putty.org/)
- NodeMCU Flasher ([Win32](https://github.com/nodemcu/nodemcu-flasher/blob/master/Win32/Release/ESP8266Flasher.exe) / [Win64](https://github.com/nodemcu/nodemcu-flasher/blob/master/Win64/Release/ESP8266Flasher.exe))

Una vez hemos instalado todo podemos comenzar a toquetear la placa.

## Instalar Firmware

Si bien este paso es hasta cierto punto opcional, conviene saber como instalar firmware en nuestros dispositivo, así como conocer como funciona la gestión de *firmwares*.

Actualmente la recomendación por parte del equipo de **NodeMCU** es usar [**NodeMCU PyFlasher**](https://github.com/marcelstoer/nodemcu-pyflasher), si bien, los resultados que yo he tenido no son los más satisfactorios, y por ello, en el paso anterior se ha instalado la herramienta **NodeMCU Flasher**, que aunque obsoleta funciona perfectamente.

Con esta herramienta se pueden instalar distintos firmware en nuestro dispositivo. Estos firmware se pueden componer a nuestro gusto; la razón, el espacio disponible en la placa es muy limitado y conviene incluir solo las partes que nos interesen. Para ello se han creado distintos módulos cuya descripción puede consultarse en la [documentación oficial](https://nodemcu.readthedocs.io/en/master/).

Para crear nuestro firmware personalizado pueden usarse varias herramientas. Yo recomiendo [**NodeMCU custom cloud builds**](https://nodemcu-build.com/). Una herramienta que generará compilaciones en la nube y en la que podemos personalizar como queramos varios parámetros importantes.

Una vez que hayamos generado nuestro firmware, tendremos dos archivos disponibles con extensión *.bin*, un **integer** y un **float**. La diferencia entre ambos está en el tipo de operaciones que se pueden realizar. En la versión *integer*, solo se pueden realizar operaciones con enteros y en la *float* operaciones con enteros y números en coma flotante. La versión *integer* es más restringida pero ocupa menos memoria y consume menos recursos. No es una cuestión de vida o muerte. Puedes instalar la versión del firmware integer, y si lo necesitas la *float*. El tiempo de instalación no es tan alto.

Abrimos **NodeMCU Flasher** y seleccionamos el puerto *COM* donde está conectado el dispositivo por **USB**. ¿Cómo saber que puerto **COM** es? Nos vamos a *Sistema -> Administrador de dispositivos -> Puertos (**COM y LPT**)* y buscamos nuestro dispositivo. Seguido del nombre, tendremos entre paréntesis el puerto al que está conectado.

![Administrador de Dispositivos](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/administrador-dispositivos.png)

En la pestaña *Config*, hacemos click en el primer engranaje de la lista y seleccionamos el firmware que queramos instalar.

![NodeMCU Flasher Config](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/nodemcu-flasher-config.png)

Volvemos a la pestaña *Operation* y hacemos click en **Flash(E)**. Si la operación ha sido exitosa, nos saldrá la siguiente pantalla y ya podremos empezar a usar nuestro **NodeMCU**. Recomiendo reiniciar el dispositivo después de la operación.

![NodeMCU Flasher Operation](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/nodemcu-flasher-operation.png)

## Comenzando a programar

Ya por fin, después de toda esta introducción, podemos meter mano al cacharro.

Para probar, vamos a usar **Putty** e ir introduciendo comandos a la placa. Todos estos comandos funcionan en tiempo real, si por algún motivo la placa se reiniciara o se desconectara de la corriente, todos los cambios se perderían.

Abrimos **Putty**, modo **serial**, indicamos el **puerto**, velocidad de **9600 baudios** y hacemos click en abrir. Si todo ha ido bien, **NodeMCU** nos dará la bienvenida.

![Putty](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/putty.png)

![Terminal Putty](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/consola-putty.png)

Ya podemos introducir comandos. Os dejo algunos ejemplos para que probéis:

### Ejemplo 1 (Encender y apagar un led)

![Esquema Ejemplo 1](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/ejemplo-encender-apagar-led.png)

```lua
gpio.mode(1,gpio.OUTPUT) -- Configuramos el pin para salida de pulsos
gpio.write(1,gpio.HIGH) -- El LED se apaga
gpio.write(1,gpio.LOW) -- El LED se enciende
gpio.write(1,gpio.HIGH) -- El LED se apaga
```

### Ejemplo 2 (WIFI modo cliente)

Conectarnos a una red **WIFI** en modo cliente para poder realizar tareas como realizar una llamada a un servidor.

```lua
print(wifi.sta.getip()) --nil
wifi.setmode(wifi.STATION)
wifi.sta.config("SSID","password") -- sustituir SSID y password por los de tu casa
print(wifi.sta.getip()) --192.168.1.43
```

### Ejemplo 3 (Temperatura y humedad con DHT11 / DHT22)

Para este ejemplo, asegurarse de tener incluida en el **Firmware** la librería **dht**.

- El sensor DHT11 puede encontrarse en [Amazon](http://amzn.to/2k48BQp).
- El sensor DHT22 puede encontrarse en [Amazon](http://amzn.to/2k3JF00).

![Esquema Ejemplo 3](/assets/posts/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/ejemplo-temperatura-humedad-dht.png)

El siguiente código bloquea el hilo de ejecución de la placa debido al **bucle infinito**. Para poder pararlo es necesario reiniciar la placa.

```lua
while 1 do
    status,temp,hum,tempDec,humDec = dht.read(5) -- Leemos los datos y los metemos en variables
    if(status == dht.OK) then
        print("temperatura: "..temp)
        print("humedad: "..hum)
        print("temperatura en decimales: "..tempDec)
        print("humedad en decimales: "..humDec)
    end
    tmr.delay(10000000) -- Esperamos 10,000,000 us = 10 segundos
end
```
