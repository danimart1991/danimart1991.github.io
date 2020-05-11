---
title: "Seguimiento de dispositivos en Home Assistant"
header:
  image: /assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 29
permalink: /es/seguimiento-de-dispositivos-en-home-assistant/
---

[***Home Assistant***](https://www.home-assistant.io/) tiene un tipo de integraciones llamado [***Device Tracker***](https://www.home-assistant.io/integrations/device_tracker/), estas integraciones están destinadas a controlar la localización de los distintos dispositivos que vinculamos a nuestro servidor.

Dependiendo de la integración, esta "localización" puede cambiar completamente. Por poner algunos ejemplos, se puede obtener la posición *GPS* de un dispositivo, indicar que dispositivos están conectados a Internet usando la red *Wi-Fi* de casa, un listado de aquellos visibles por un controlador *Bluetooth* conectado al servidor...

## Tipos de seguimiento según uso

En general, los tipos de uso que se les suelen dar a este tipo de integraciones son los siguientes:

- **Exterior**: Información de seguimiento de un dispositivo fuera de casa. Normalmente usando el rastreador *GPS* que posee el dispositivo, se envía su posición. *Home Assistant* dispone de mapa para ver el punto donde se encuentra.
- **Interior**: Información de seguimiento de un dispositivo dentro de casa. Útil por ejemplo cuando se quiere saber en qué habitación de encuentra un dispositivo.
- **Sensor binario**: Informa solo si el dispositivo está dentro o fuera de casa. Muy utilizado para saber cuándo un dispositivo que está en una posición fija dentro de casa está encendido (en casa) o apagado (fuera de casa).

## Probando integraciones

Dado el alto número de integraciones de seguimiento de dispositivos (detección de presencia) que dispone *Home Assistant*. Lo mejor que se puede hacer es probar las integraciones que tengamos disponibles desde la categoría [***Presence Detection***](https://www.home-assistant.io/integrations/#presence-detection).

Por mi parte he realizado pruebas con distintas integraciones intentando orientar su uso según la conveniencia del dispositivo.

![Pruebas Integraciones Device Tracker](/assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/pruebas-device-tracker.jpg)

### Bluetooth Tracker

Tanto [*Bluetooth Tracker*](https://www.home-assistant.io/integrations/bluetooth_tracker/), como su variante mejorada [*Bluetooth LE Tracker*](https://www.home-assistant.io/integrations/bluetooth_le_tracker/) son bastantes sencillos de instalar, basta con declararlos como plataforma en el archivo `configuration.yaml`.

```yaml
device_tracker:
  - platform: bluetooth_le_tracker
```

Utilizan el protocolo *Bluetooth* si se encuentra disponible en nuestro servidor para localizar dispositivos dentro del radio de acción. Por tanto, su uso será de tipo interior. Al iniciar el servidor detectará nuevos dispositivos y los agregará al archivo `known_devices.yaml`, no hay forma de acotarlo, así que si el rango de acción se encuentra cerca de una calle transitada, en poco tiempo tendrás el almacenamiento del servidor lleno de dispositivos que no te interesan.

Como punto a favor, la precisión es bastante alta y al ser una detección pasiva, los dispositivos no suelen "ocultarse" al radar. Además, en otros sistemas como *ESPHome*, se pueden colocar dispositivos de detección en cada habitación para así saber en cada momento la habitación donde se encuentra el dispositivo a seguir y no solo eso; desde *ESPHome* se pueden seguir solo aquellos dispositivos que queramos y no se realiza un escaneado de dispositivos ajenos.

### Life360

[*Life360*](https://www.home-assistant.io/integrations/life360/) es un servicio de localización de dispositivos móviles de tipo exterior. Ofrece una aplicación bastante completa con registro de usuarios y posibilidad de compartir ubicaciones y otros datos con otros usuarios. Su uso es muy sencillo y permite su integración de manera visual o mediante *YAML*.

Por contra, al ser un servicio de terceros, los datos de ubicación son registrados en los servidores de *Life360*.

### OwnTracks

[*OwnTracks*](https://www.home-assistant.io/integrations/owntracks/) es la alternativa *Open Source* a *Life360*. Si bien no tiene tantas características como su competidora, los datos solo se envían desde las aplicaciones móviles a un servidor propio, no se usa ningún servicio de terceros.

*Home Assistant* mediante la declaración (de manera visual o con *YAML*) de la integración *OwnTracks*, creará un servidor de *OwnTracks Webhook* donde recibir la información de manera segura mediante *HTTP*. También dispone de la opción *MQTT* si se dispone de un servidor configurado aparte.

> **@ChimoC** desde los grupos de domótica de *Telegram*, reporta un pequeño fallo con la integración de *OwnTracks*. Al realizar la configuración, *Home Assistant* mostrará una dirección *Web* para incluirla en la aplicación de *OwnTracks* de tipo: `https://TU_USER.duckdns.org/api/webhook/API_KEY` a la que es necesario añadir el puerto del servidor para que funcione: `https://TU_USER.duckdns.org:8123/api/webhook/API_KEY`.

### Google Maps

La integración con [*Google Maps*](https://www.home-assistant.io/integrations/google_maps/) se basa en aprovechar las *Cookies* donde se envían y reciben las posiciones *GPS* de un dispositivo registrado para crear una localización dentro de *Home Assistant*. Su integración es algo complicada. Puede ser muy útil para aquellas personas que tienen instalado *Google Maps* en el móvil y no quieren instalar otra aplicación.

### iCloud

Si se usa el ecosistema de *Apple*, la integración con [*iCloud*](https://www.home-assistant.io/integrations/icloud/) puede ser muy cómoda para conocer la posición de estos dispositivos. Se puede realizar la integración de manera visual o usando *YAML*.

Como contra, por el momento, aquellas cuentas con doble factor de autenticación presentan problemas al ser configuradas.

### Mobile App

Si se usa la aplicación móvil de *Home Assistant*. Dispone de una [integración](https://www.home-assistant.io/integrations/mobile_app/) para seguimiento de dispositivos. Solo es necesario abrir la configuración de la aplicación en el menú y activar los servicios de ubicación del dispositivo. Automáticamente se vinculará con el servidor y se empezarán a enviar datos de posición.

### Nmap

[*Nmap*](https://www.home-assistant.io/integrations/nmap_tracker/) es una utilidad de escaneado de red. Lo que se consigue con esta integración de tipo interior, es ver que dispositivos están conectados a la red mediante un escaneo por *ARP* del rango que indiquemos en el archivo `configuration.yaml`. Habitualmente, la máscara de red suele ser *255.255.255.0*, lo cual indica que el rango de *IPs* disponibles para los dispositivos es de las ultimas 254 *IPs*. Esto quiere decir, que si, por ejemplo, la *IP* de puerta de enlace (del *Router*) es `192.168.1.1`, el rango de *IPs* a buscar será desde `192.168.1.2` a `192.168.1.255`. Este rango es indicado con `192.168.1.1/24` o `192.168.1.1-255`.

```yaml
device_tracker:
  - platform: nmap_tracker
    hosts: 192.168.1.1/24
```

Como en otras integraciones. Los dispositivos encontrados serán guardados en `known_devices.yaml` y asociados con *Nmap* mediante su dirección *MAC*.

### Ping

La integración [*Ping*](https://www.home-assistant.io/integrations/ping/) actúa de manera parecida a *Nmap*, pero está orientada a dispositivos que no pueden ser accesibles por *ARP*. Otro punto es que *Ping* no realiza un escaneado de todo el rango de *IPs*, sino que se indica un listado de *IDs* e *IPs* en las que se va a realizar el rastreo mediante *ICMP*.

```yaml
device_tracker:
  - platform: ping
    hosts:
      my_iphone: 192.168.1.3
```

De igual modo, los dispositivos se agregarán a `known_devices.yaml`.

> Para poder usar la integración *Ping* es necesario que el dispositivo a seguir tenga una *IP* fija asignada.

## Los dispositivos fantasmas

Las integraciones relacionadas con seguimiento de dispositivos son *"Best Effort"*, es decir, van a intentar dar lo mejor de sí mismas y darnos datos precisos, pero no siempre van a conseguirlo.

De este modo, si por ejemplo configuramos *Nmap* y *Ping* para un móvil, veremos que da resultados contradictorios.

![Dispositivos Fantasmas](/assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/dispositivos-fantasmas.jpg)

Esto es debido a que algunos dispositivos se desconectan de las redes *Wi-Fi* para ahorrar batería. Otros, aunque los dejemos en *Standby* (apagados, pero con luz), se quedan conectados a la red, por lo que los sensores nos indicarán que está "encendido".

La mejor forma de solucionar esto es usar las integraciones completas de *Home Assistant*. Por ejemplo, en el caso de las televisiones *LG*, yo uso la integración [*webOS TV*](https://www.home-assistant.io/integrations/webostv). Son mucho más precisas, y además aportan más funcionalidades. Otra manera es probar diferentes integraciones hasta dar con la más precisa para ese dispositivo.

## Zonas y áreas

Una vez obtenida la posición de los dispositivos, se pueden asignar etiquetas a las posiciones. Por ejemplo, una zona que indique la oficina donde se trabaja para así, *Home Assistant* sepa cuando estamos en el trabajo, o asignar dispositivos a habitaciones para tener una gestión más sencilla de los dispositivos en el servidor.

### Zones

Las [**zonas**](https://www.home-assistant.io/integrations/zone/) están pensadas para el primer caso. Desde la sección [Mapa](https://www.home-assistant.io/integrations/map/), o desde `Configuración -> Zonas`, o desde el archivo `zones.yaml` se pueden crear zonas con una posición *GPS* y un rango. Cuando el dispositivo se encuentre en esa zona, su estado de seguimiento cambiará al identificador de la zona.

![Editor de Zonas](/assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/zonas.jpg)

### Áreas

Las áreas funcionan de manera parecida a las zonas, salvo que actúan de manera interna a la casa. Normalmente se usan para definir las distintas habitaciones y asignar dispositivos a ellas. Solo pueden configurarse de manera visual desde `Configuración -> Áreas`.

![Editor de Áreas](/assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/areas.jpg)

## Asociando dispositivos a personas

Una vez posicionados los dispositivos, se pueden vincular a los usuarios de *Home Assistant* mediante la creación de entidades tipo [***Persona***](https://www.home-assistant.io/integrations/person/).

Estas entidades tienen uno o varios *device_tracker* asociados, de tal manera que se complementen y den la situación más precisa para el usuario y con ello saber en todo momento donde se encuentra.

![Seguimiento de Personas](/assets/posts/es/seguimiento-de-dispositivos-en-home-assistant/seguimiento-personas.jpg)

Basta con declarar la persona desde `Configuración -> Personas` (o mediante *YAML*) y asignar las entidades de tipo *device_tracker* que se quieran usar. *Home Assistant* seguirá las siguientes reglas:

1. Si hay rastreadores fijos (por ejemplo, *Nmap* o *Bluetooth*) con el estado "Casa", el rastreador más recientemente actualizado será el que determine la posición.
2. Si hay rastreadores de tipo *GPS*, se utilizará el rastreador más recientemente actualizado.
3. En cualquier otro caso, el último rastreador con el estado "Fuera de casa" será usado.

En otras palabras. Si estás en "casa", la posición la determinarán primero las integraciones de rastreadores fijos (si están configurados) y luego se usarán los *GPS*. Si estás "fuera de casa", la posición la determinarán primero los de tipo *GPS* y luego los fijos.

> Aunque al tener varios dispositivos de seguimiento configurados la precisión aumenta, no se recomienda el uso de estas entidades para operaciones críticas. Un uso correcto es el de encender o apagar la calefacción según la posición de la persona, pero no conviene usarlo para alarmas o puertas hasta realizar bastantes comprobaciones y pruebas.

## Conclusión

Se ha realizado un recorrido por el seguimiento de dispositivos, su configuración y problemas. Solo queda analizar todos los dispositivos de la casa y agregarlos al sistema para poder llevar un mejor control de todo el hogar. Además, se podrán vincular esas integraciones a personas para poder saber dónde se encuentran en cada momento.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
