---
title: "Calibrar Sensores en ESPHome"
header:
  image: /assets/posts/es/calibrar-sensores-esphome/header.png
categories:
  - Tutorial
  - ESPHome
  - Domótica
tags:
  - ESPHome
  - Enchufe
  - Inteligente
  - Sensores
  - Tutorial
lang: es
ref: 42
permalink: /es/calibrar-sensores-esphome/
---

Tanto si has cambiado el firmware de un dispositivo _Wi-Fi_ con un chip de tipo _ESP8266/ESP32_, como si has creado un dispositivo propio con distintos sensores, te habrás dado cuenta de que **las mediciones de algunos sensores no son muy reales**. Muchos sensores no requieren una configuración extra por nuestra parte, simplemente funcionan y dan su valor, pero otros, pueden venir con una ligera desviación de fábrica.

Un caso muy típico son los enchufes _Wi-Fi_ con medidor de consumo o los sensores de temperatura y humedad. Con el firmware original vienen calibrados para evitar la desviación del sensor, pero, al cambiar su sistema a [**_ESPHome_**](https://esphome.io/) perdemos esa calibración. Tranquilo, con _ESPHome_ es muy sencillo [ajustar estos sensores para dar un resultado más preciso](https://esphome.io/components/sensor/index.html#sensor-filters), pero requiere tiempo y paciencia. **Voy a explicar mi método para calibrar poniendo de ejemplo un enchufe con medidor de consumo**.

## Antes de empezar

Vamos a trabajar directamente sobre el dispositivo con _ESPHome_, por tanto, no es necesario [_Home Assistant_](https://www.home-assistant.io/) o cualquier otro software de gestión domótica, aunque puede ayudar como veremos más adelante.

**¿Qué vamos a necesitar?**

- _ESPHome_ (_Dashboard_) para editar el archivo `.yaml` con el código fuente, compilarlo y ver los _Logs_ (registros) que genera.
- Un dispositivo con firmware _ESPHome_ y el sensor que queramos calibrar. Es necesario que la definición del sensor en el archivo `.yaml` esté limpia de [modificadores](https://esphome.io/components/sensor/index.html#sensor-filters) para obtener el valor real que ofrece el sensor.
- Un dispositivo analógico o digital de referencia.
- Diferentes referencias.

### Dispositivos de referencia

Este dispositivo dependerá del tipo de sensor a calibrar.

En el caso de los enchufes, puedes comprar un medidor de consumo [aquí mismo](https://amzn.to/3Bi6e5d), o usar cualquier otro enchufe ya calibrado, o incluso [un PZEM-004T](https://amzn.to/3zc0tUP) con la pinza colocada para medir el mismo consumo que el dispositivo que queremos calibrar. Dependiendo del dispositivo de referencia, el montaje será distinto.

![Dispositivo de Referencia - Enchufe](/assets/posts/es/calibrar-sensores-esphome/dispositivo-referencia-enchufes.jpg)

Para otro tipo de sensores como puede ser temperatura o humedad, un termómetro analógico o incluso digital puede servirnos de referencia.

![Dispositivo de Referencia - Termómetro](/assets/posts/es/calibrar-sensores-esphome/dispositivo-referencia-termometro.jpg)

### Diferentes referencias

Como veremos a continuación, **lo principal es obtener diferentes medidas para que el propio _ESPHome_ ajuste la desviación todo lo posible**. Cuantas más referencias, mejor definición.

Por ejemplo, para enchufes, distintos dispositivos que generen distintos consumos como un secador de pelo (que además tiene distintas posiciones), bombillas de filamento, una tostadora… Siempre con cuidado de no superar el máximo que aguanta el sensor.

Para sensor de temperatura, se puede usar líquidos o ambientes a distintas temperaturas. Para humedad, un humidificador...

Tanto en unos como en otros, el dispositivo de referencia será el que nos diga el valor real.

## Planteamiento

Como ya hemos mencionado, la idea detrás de conseguir el calibrado es la obtención de referencias del sensor respecto a una medición real en otro sensor ya calibrado. **Cuantas más mediciones hagamos, más referencias y más exacto será.**

Además, **es muy importante la realización de medidas en todos los rangos posibles**, es decir, como mínimo medidas en cada extremo posible de medición del sensor y en la mitad del rango, con esto nos aseguraremos de cubrir todos los posibles escenarios.

La manera de realizar estas mediciones puede diferir bastante dependiendo del dispositivo a medir y el tipo de sensor. Por ejemplo, si optamos por un enchufe, normalmente incluye tres medidas: corriente, voltaje y potencia, por lo que necesitaremos valores de referencia de todos ellos.

Si tenemos de referencia un medidor de consumo, convendrá tener cerca la visualización de resultados del medidor y poder así comparar con las medidas recogidas por el sensor que queremos calibrar. Por ejemplo, podemos usar una webcam y así en la propia pantalla del ordenador ver ambos valores de un vistazo y en la misma marca temporal. Si por otro lado, como es mi caso, disponemos de un _PZEM-004T_ con _ESPHome_, podremos tener los _Logs_ de ambos dispositivos en pantalla partida y así de un vistazo ver ambos.

## Obteniendo referencias

Tal y como hemos planteado, **la idea es obtener diferentes referencias e ir apuntando todas ellas por ejemplo en una [_hoja de cálculo_ como esta](/assets/posts/es/calibrar-sensores-esphome/comparativa-excel.xlsx)**.

De nuevo hay que recordar que es muy importante obtener los casos extremos del rango que aguante el sensor, así como la referencia nula (en el caso de un enchufe, cuando el consumo es 0A/0W). **Un sensor puede generar valores sobre si mismo o sobre el dispositivo de referencia**, es decir, el propio enchufe a calibrar genera consumo al ser un dispositivo electrónico, sobre el enchufe medidor de consumo que estamos usando de referencia real, y esa diferencia es importante apuntarla.

![Comparación referencias en ESPHome](/assets/posts/es/calibrar-sensores-esphome/comparacion-esphome.jpg)

El proceso que sigo es el de tener ambos dispositivos en pantalla, obligar una referencia (por ejemplo, encender el secador de pelo en una posición concreta y esperar unos segundo a que se estabilice) y a continuación, realizar y guardar una captura de pantalla donde se aprecien ambos sensores con sus respectivos atributos. Cuando tengo varias capturas, busco la correlación entre ambos (en los _Logs_ de _ESPHome_ basta con buscar el mismo tipo de atributo coincidente en la misma marca de tiempo en ambos _Logs_), y lo voy apuntando.

![Comparación referencias Excel](/assets/posts/es/calibrar-sensores-esphome/comparacion-excel.jpg)

## La correlación

A continuación, con la propia hoja de cálculo podemos sacar fácilmente una correlación entre ambos valores dividiendo el valor real sobre el valor desviado.

En _ESPHome_ hay diversas formas de indicar esta desviación al sensor o atributo:

- Si los valores correlacionados difieren bastante, o si queremos una medición más precisa, podemos usar el filtro `calibrate_linear`, indicando cada una de las referencias que hemos realizado.

  ```yaml
  current:
    id: ${device_name}_current
    name: ${friendly_name} Current
    icon: mdi:current-ac
    unit_of_measurement: A
    filters:
      - calibrate_linear:
          # Map 0.0 (from sensor) to 0.0 (true value)
          - 0.0 -> 0.0
          - 0.131 -> 0.1442
          - 0.418 -> 0.42035
          - 0.603 -> 0.5981
          - 3.054 -> 3.03078
          - 4.035 -> 3.9948
          - 6.085 -> 6.02167
          - 7.346 -> 7.2887
          - 13.093 -> 13.05151
  ```

  > **Nota:** Aunque en la hoja de cálculo no incluyo la correlación con 0 para evitar una media errónea (ya que en mi caso la referencia es igualmente 0). Es necesario incluirlo en el listado.

- Si los valores correlacionados son muy próximos entre sí, o si no nos importa que la medición tenga un pequeño desvío, podemos usar otro tipo de filtros que modifiquen el valor obtenido por el sensor por la media de valores correlacionados, por ejemplo, `multiply`.

  ```yaml
  current:
    id: ${device_name}_current
    name: ${friendly_name} Current
    icon: mdi:current-ac
    unit_of_measurement: A
    filters:
      - multiply: 0.993793
  ```

> **Nota:** En ocasiones la correlación no es lineal. Para estos casos, [_ESPHome_ dispone de otros filtros](https://esphome.io/components/sensor/index.html#sensor-filters) con los que realizar otro tipo de operaciones. Por ejemplo, tengo un sensor de temperatura que marca siempre dos grados menos, por lo que utilizo el filtro `offset: 2.0`.

## Comprobando los resultados

Por último, solo queda realizar la misma técnica que hemos usado para obtener referencias pero esta vez para comprobar que tanto el dispositivo de referencia como el dispositivo que hemos calibrado ofrecen los mismos resultados, en el caso de tener ambos en _Home Assistant_ o de calibrar varios dispositivos a la vez (cuidado con apilar unos sobre otros ya que los propios enchufes generan consumo), se pueden ver las diferencias entre unos y otros fácilmente.

![Comparación de Sensores en Home Assistant](/assets/posts/es/calibrar-sensores-esphome/comparacion-sensores-ha.jpg)

Muchos de **los sensores tienen pequeños desvíos difíciles de arreglar**, pero en caso de ser significativamente altos, conviene empezar el proceso de nuevo por si se ha realizado alguna medición errónea.

## Conclusión

En pocos pasos, pero invirtiendo unas cuantas horas, **hemos calibrado los sensores de los dispositivos con _ESPHome_**. Con ello será mucho más sencillo poder realizar automatizaciones o controlar el consumo o temperatura de nuestro hogar.

No dudes en preguntar o aportar tu experiencia siguiendo este artículo en los comentarios.

Este y otros artículos complementan la documentación del [**repositorio de _GitHub_**](https://github.com/danimart1991/esphome-config) donde se encuentra disponible toda la configuración de mis dispositivos _ESP8266_/_ESP32_ con _ESPHome_.

## Ver también

- [Diego Castro (Pandora Box) - Cómo calibrar un Sonoff Pow R2](https://diegocastroviadero.com/post/como-calibrar-un-sonoff-pow-r2/)
- [Frenck - Calibrating an ESPHome flashed power plug](https://frenck.dev/calibrating-an-esphome-flashed-power-plug)
- [ESPHome - Sensor Filters](https://esphome.io/components/sensor/index.html#sensor-filters)
