---
title: "Controlar dispositivos inteligentes desde Tuya Smart Life"
header:
  image: /assets/posts/es/controlar-dispositivos-inteligentes-desde-tuya-smart-life/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 21
permalink: /es/controlar-dispositivos-inteligentes-desde-tuya-smart-life/
---

[***Tuya***](https://www.tuya.com/) es una empresa dedicada al mundo del [Internet de las cosas (*IoT*)](https://es.wikipedia.org/wiki/Internet_de_las_cosas). Tiene multitud de productos, aplicaciones, servicios... creando un ecosistema propio y completo.

La explosión de la marca vino al liberar los esquemas electrónicos y su ecosistema para que cualquier empresa pudiese desarrollar sus propios dispositivos basados en los que *Tuya* había creado previamente, y de paso unificar todos ellos en una misma plataforma. Los consumidores ganaban al solo tener que instalar una aplicación y con precios más ajustados debido a la competencia, y las empresas podían desarrollar productos inteligentes de manera más rápida, sencilla y económica.

![Ecosistema Tuya](/assets/posts/es/controlar-dispositivos-inteligentes-desde-tuya-smart-life/image01.jpg)

Todo esto repercute en que si has comprado un producto inteligente (sobre todo marcas chinas), lo más seguro es que utilice la aplicación ***Tuya Smart Life*** (o derivadas). Su uso depende del dispositivo, pero en la mayoría de los casos, es muy sencillo agregar y usar un nuevo dispositivo. Como valor añadido, es compatible con [*Google Assistant*](https://assistant.google.com/) y [*Alexa*](https://es.wikipedia.org/wiki/Amazon_Alexa).

## Experiencia

Entre otros dispositivos *Tuya*, dispongo de un enchufe inteligente [*Blitzwolf SHP2*](https://amzn.to/2nUEWAO) que ofrece encendido y apagado desde la aplicación, un botón físico, y medidor de consumo. Añadirlo a la aplicación fue tan sencillo como enchufarlo, dar al botón de añadir dispositivo en la aplicación y esperar.

![Enchufe configurado con Tuya Smart Life](/assets/posts/es/controlar-dispositivos-inteligentes-desde-tuya-smart-life/image02.jpg)

Pros:

- La aplicación es realmente rápida, fiable, potente y bastante bien diseñada.
- Podremos crear temporizadores, escenas y automatizaciones.
- En este modelo, el botón físico hace que podamos encender y apagar el aparato tanto físicamente como desde el móvil. Esto suele ser un problema en dispositivos inteligentes pues al necesitar siempre corriente, el interruptor físico de nuestro aparato queda inservible y de no disponer de un interruptor en el dispositivo inteligente, solo podríamos encender y apagar desde el móvil.
  > Este problema se da principalmente en las bombillas inteligentes que nos obligan a dejar siempre encendido el interruptor de pared y controlar su encendido exclusivamente desde una aplicación móvil. Razón por la que no suelo recomendarlas.
- Si así lo deseamos, es compatible con [*Home Assistant*](https://www.home-assistant.io/).

Contras:

- Estamos obligados a que todos nuestros dispositivos sean compatibles con *Tuya* para así evitar tener más de una aplicación instalada en nuestro móvil.
- Para cualquier acción, en la mayoría de los dispositivos *Tuya*, estamos obligados a hacer uso de sus servidores. Esto significa que, al encender un aparato desde el móvil, se envía una petición a sus servidores, y los servidores envían una señal al enchufe para que se encienda. Esto da problemas de disponibilidad si el servidor se cae, o de latencia si el servidor tarda en realizar la acción. (En otro artículo veremos cómo se puede evitar).

## Uso con *Home Assistant*

> A fecha de publicación de este artículo, [*Home Assistant* da soporte para bastantes dispositivos y funcionalidades de *Tuya*](https://www.home-assistant.io/integrations/#search/tuya) tales como interruptores, enchufes, escenas... sin embargo otras funcionalidades como medidor de consumo no se encuentran disponibles. Por otra parte, usar la integración en *Home Assistant* no anula la aplicación oficial, por lo que se pueden usar ambas.

El uso de la plataforma *Tuya* en *Home Assistant* es bastante sencillo. Solo necesitamos el **código del país** que indicamos al registrarnos en la aplicación, así como el **usuario** y la **contraseña**. Todos estos datos los añadimos al archivo `configuration.yaml` [como ya hemos hecho en otras ocasiones](/es/configura-home-assistant-editando-sus-archivos/) mediante el siguiente código:

```yaml
tuya:
  username: USUARIO
  password: PASSWORD
  country_code: CÓDIGO_PAÍS #country_code: 34 para España
  platform: smart_life
```

Dado que existen varias aplicaciones disponibles para dispositivos *Tuya*, en el nodo `platform` debemos indicar en [cual nos hemos registrado](https://www.home-assistant.io/components/tuya/#platform).

Reiniciamos nuestro servidor de *Home Assistant*, y dependiendo de cómo tengamos configurado [*Lovelace*](https://www.home-assistant.io/lovelace/), tendremos disponibles los distintos dispositivos y escenas configurados previamente en la aplicación *Tuya Smart Life* en nuestra interfaz de *Home Assistant*.

![Enchufe configurado en *Tuya* en *Home Assistant*](/assets/posts/es/controlar-dispositivos-inteligentes-desde-tuya-smart-life/image03.jpg)

## Conclusión

Hemos visto como los dispositivos *Tuya* se pueden configurar fácilmente usando además una única aplicación y con el añadido de ser un ecosistema compatible con *Home Assistant*, lo que los hace unos dispositivos bastante llamativos y con un precio asequible a cualquier bolsillo.

En próximos artículos veremos además como evitar usar los servidores de *Tuya* instalando un *firmware* personalizado. Muchos de estos dispositivos se basan en el [*chip esp8266*](/es/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/) que podemos programar para aprovechas todas sus funcionalidades.
