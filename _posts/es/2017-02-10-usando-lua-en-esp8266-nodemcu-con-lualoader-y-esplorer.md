---
title: "Usando LUA en ESP8266 / NodeMCU con LuaLoader y ESPlorer"
header:
  image: /assets/posts/es/usando-lua-en-esp8266-nodemcu-con-lualoader-y-esplorer/header.jpg
tags: [ Español,  ESP8266, LUA, Arduino, NodeMCU, LuaLoader, ESPlorer ]
categories: [ IoT ]
lang: es
ref: 4
permalink: /es/usando-lua-en-esp8266-nodemcu-con-lualoader-y-esplorer/
---

Continuando con una serie de post orientados a la programación sobre la placa **NodeMCU**,un dispositivo bastante usado en el mundillo **IOT** para todo lo relacionado con **WIFI**, y que está disponible en [**Amazon por apenas 8€**](http://amzn.to/2lp3HPG), además incluye el chip **WIFI ESP8266**, uno de los más comunes y versátiles. Como decía vamos a aventurarnos en el uso de dos herramientas bastante usadas para ejecutar comandos y cargar scripts en lenguaje **LUA** en nuestro dispositivos. Puede usarse el **SDK** incluido en **ESP8266** para programar, pero [hay varias razones para no hacerlo](http://internetofhomethings.com/homethings/?p=426).

Antes de nada. Si estás empezando con esta placa o chip, te recomiendo que visites mi anterior post sobre [como comenzar a trabajar con este dispositivo](http://danielmartingonzalez.com/conociendo-esp8266-nodemcu-el-modulo-wifi-para-iot/). Puede usarse cualquiera de los scripts de código disponibles en este post en cualquiera de los programas mencionados en este post.

Una vez que hemos visto los puntos básicos y que hemos probado algunos comandos por consola, hemos de saber que disponemos de un par de herramientas que son capaces de interactuar de una manera mucho más simple y usable con **NodeMCU** y que es capaz de cargar scripts para ser ejecutados durante el inicio o ser cargados a voluntad. Ambos son muy parecidos, el uso de uno u otro dependerá de nuestras preferencias y facilidad de uso. A continuación os explico un poco cada uno.

## LuaLoader

![LuaLoader](/assets/posts/es/usando-lua-en-esp8266-nodemcu-con-lualoader-y-esplorer/lualoader.jpg)

Desarrollado por **Peter Jennings** para *Windows*, y disponible para su descarga gratuita en **[Benlo](http://benlo.com/esp8266/)**. Es un programa simple y que cumple con su propósito, no busca complejidad y dificultad añadida.

Para conectar con el dispositivo basta con ir a la pestaña *Settings -> Comm Port Settings*, y seleccionar el puerto de nuestro **NodeMCU**. Una vez indicado, hacemos click en la pestaña *Connect* y ya estaremos conectados.

Incluye funcionalidad muy útil como:

- Envío de comandos.
- Botón *Heap* para liberación de memoria.
- Botón *Restart* para reiniciar el dispositivo.
- Panel *GPIO* para la configuración de pines en el dispositivo, tanto lectura, escritura y repetición de eventos.
- Panel de configuración de Baudios.
- Panel de configuración *WIFI*. Se puede crear un punto de acceso en el dispositivo, o que el dispositivo se conecte en modo cliente a una red *WIFI*.
- Panel para subir ficheros *LUA* al dispositivo, mostrar los ficheros que tiene el dispositivo, o subir el contenido que estamos escribiendo en consola. El dispositivo carga al inicio el fichero **init.lua**. Subiendo un fichero con ese nombre nos aseguramos de que cargue ese script nada más iniciar.

Todo ello de manera visual, sin necesidad de introducir comandos. Lo que nos salvará en muchos casos de tener que aprendernos líneas de código, además de añadir velocidad a la programación.

No voy a pararme a explicar cada función del programa ya que es bastante visual. El propio programa incluye ayuda bastante detallada. Si tienes cualquier duda de alguna función, puedes escribirme un comentario y estaré encantado de contestar.

## ESPlorer

![ESPlorer](/assets/posts/es/usando-lua-en-esp8266-nodemcu-con-lualoader-y-esplorer/esplorer.jpg)

Al contrario que *LuaLoader*, **ESPlorer** está desarrollado en **JAVA**, por lo que su uso no está restringido a *Windows*. Por contra, debemos tener instalado [**JAVA**](http://java.com/download) (*SE* versión 7 o superior) para que funcione. Si bien es un entorno de desarrollo más completo que *LuaLoader*, la funcionalidad es muy parecida en muchos casos. Aunque más avanzada y con más opciones. Puede descargarse en la **[página oficial](https://esp8266.ru/esplorer/)**, y ayudar a la comunidad en su [página de **GitHub**](https://github.com/4refr0nt/ESPlorer).

Del forma parecida a *LuaLoader*, en el panel derecho, basta con seleccionar el puerto **COM** en el que está conectado nuestro dispositivo, hacer click en **Open**, y nos conectaremos al dispositivo.

Además de todo lo incluido en *LuaLoader*, **ESPlorer** contiene funciones tan útiles como:

- Repertorio de Snippets para acceso rápido a funciones muy usadas por el programador.
- Realzado de sintaxis *LUA* y *Python*.
- Temas para personalizar el entorno de desarrollo.
- Comandos deshacer y rehacer.
- Autocompletado de código (*Ctrl+Espacio*).
- Envío inteligente de datos a *ESP8266*.
- Snippets de código.
- Log detallado.
- …

## Conclusión

Ambos programas son muy capaces para principiantes en la placa. Si bien, **[ESPlorer](https://esp8266.ru/esplorer/)** es más avanzado y dispone de muchas más opciones que **[LuaLoader](http://benlo.com/esp8266/)**, además es multiplataforma, lo que lo hace mejor a mi parecer. Por contra, es necesario tener instalado **[JAVA](http://java.com/download)** para hacerlo funcionar.

Os animo a probar ambos y sacar vuestras propias conclusiones. Cualquier duda o comentario será agradecido.

Por último, hay varias razones para [no usar ninguno de estos programas](http://internetofhomethings.com/homethings/?p=424). Como alternativa, el uso de [**Arduino IDE**](https://www.arduino.cc/en/Main/Software) que veremos en próximos post. En cualquier caso, es muy posible que ambos programas sean más que suficiente para muchos de los proyectos que queramos llevar a cabo.
