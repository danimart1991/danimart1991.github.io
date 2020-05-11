---
title: "Notificaciones de Home Assistant en Telegram"
header:
  image: /assets/posts/es/notificaciones-de-home-assistant-en-telegram/header.jpg
categories:
  - IoT
  - Home Assistant
  - Telegram
lang: es
ref: 22
permalink: /es/notificaciones-de-home-assistant-en-telegram/
---

Una vez hemos configurado varios dispositivos y entidades en [***Home Assistant***](https://www.home-assistant.io/), llega el momento de recibir avisos si estos tienen problemas, o si simplemente queremos conocer el estado de alguno de ellos. Si bien podemos configurar varios servicios con este propósito, creo que [***Telegram***](https://telegram.org/) es de los más sencillos y potentes.

## *Bots* en *Telegram*

La forma que tenemos de comunicar *Home Assistant* con *Telegram* es mediante un [***bot***](https://core.telegram.org/bots). Que no deja de ser una aplicación con la que podremos comunicarnos para realizar diferentes acciones. Podremos crear una conversación directamente con el *bot*, o incluirlo en un grupo para realizar estas acciones o que nos envíe notificaciones.

Para crear este *bot*, aunque hay distintas maneras, una sencilla es usar el *bot* ***@BotFather*** para crear y configurar paso a paso el nuestro propio.

1. Desde *Telegram*, buscamos ***@BotFather*** e iniciamos una conversación con él.

    ![Inicio conversación con @BotFather](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image01.jpg)

2. Ejecutamos el comando `/newbot`. Nos pedirá un nombre, que será el nombre visible que aparecerá mientras hablamos con nuestro *bot*, y un usuario, que será el identificador del *bot* y debe terminar siempre con `bot`.

    ![Creación de bot con @BotFather](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image02.jpg)

Una vez completado, ya tendremos creado nuestro *bot*. A continuación, se nos indicará la dirección Web con la que poder contactar con él y un *token* que usaremos más adelante.

> *@BotFather* cuenta con muchas otras opciones para configurar y potenciar nuestro *bot*.
  ![Opciones configuración de bot en @BotFather](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image03.jpg)

## Chat / Grupo para notificaciones

Lo siguiente es tener un punto donde poder recibir notificaciones del *bot*. Podemos iniciar directamente una conversación buscando el *bot* en el buscador de Telegram por el usuario que hemos indicado antes. O podemos crear un grupo con las personas que queramos se comuniquen con el *bot* y una vez creado el grupo, añadirle como un miembro más.

![Información grupo Telegram con bot](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image04.jpg)

> **IMPORTANTE:** Es muy importante agregar al bot después de crear el grupo. Si el siguiente paso no funciona, expulsar al bot del grupo y volver a agregarlo. Además es necesario iniciar el *bot*. Una vez iniciada la conversación o agregado como miembro a un grupo, mandar el mensaje `/start`.

Por último, necesitamos conocer el identificador de la conversación que acabas de crear para que así *Home Assistant* sepa la conversación a la que mandar las notificaciones a través del *bot*.

Abre en un navegador Web la dirección `https://api.telegram.org/botYOUR_API_TOKEN/getUpdates` sustituyendo `YOUR_API_TOKEN` por el *Token* que nos ha enviado antes *@BotFather* (en el ejemplo `https://api.telegram.org/bot987654321:ABCDEFGHIJK-LMNOPQRS_TUVWXYZ1234567/getUpdates`).

Como resultado obtendremos una página Web con un código parecido al siguiente. Debemos recoger el valor del campo `id` dentro del nodo `chat` (en el ejemplo `chat_id` es `-123456789`).

```json
{
  "ok": true,
  "result": [
    {
      "message_id": 1,
      "date": 1571256851,
      "chat": {
        "id": -123456789,
        "type": "group",
        "title": "Mansión Wayne",
        "all_members_are_administrators": true
      },
      "text": "/start",
      ...
    }
  ]
}
```

## Integración con *Home Assistant*

[Como en otras ocasiones](/es/configura-home-assistant-editando-sus-archivos/), para integrar un componente en *Home Assistant*, editamos el archivo `configuration.yaml` y añadimos el siguiente código:

```yaml
telegram_bot:
  - platform: polling
    api_key: YOUR_API_TOKEN
    allowed_chat_ids:
      - YOUR_CHAT_ID
```

Sustituyendo `YOUR_API_TOKEN` por el *token* del *bot* que hemos obtenido casi al principio de este artículo, y `YOUR_CHAT_ID` por el identificador del chat que acabamos de obtener en el paso anterior.

Gracias a esta integración podremos [realizar varias acciones sobre la conversación](https://www.home-assistant.io/integrations/telegram/), pero la acción más útil sin duda es poder enviar notificaciones. Estas notificaciones pasan desde enviar un simple mensaje, a mandar imágenes o botones que ejecuten acciones.

Por el momento, vamos a realizar una notificación sencilla mediante [automatización](https://www.home-assistant.io/docs/automation/) que nos mande un mensaje cuando el sensor de humedad del dispositivo [*Xiaomi Mi Flora* que configuramos hace unos artículos](/es/cuidando-tus-plantas-con-mi-flora-y-home-assistant/) este a un nivel bajo. Tenemos dos opciones:

- Por interfaz gráfica. Desde `Configuración > Automatizaciones`. Creamos una nueva automatización con el nombre que más nos guste, con la siguiente configuración:

  ![Creación de Automatización para notificación en Telegram](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image05.jpg)

- Por *YAML*. Desde el archivo de automatizaciones (`automations.yaml`) incluimos el siguiente código:

  ```yaml
  - alias: 'Notificación Telegram Mi Flora 01 Humedad'
    trigger:
      platform: numeric_state
      entity_id: sensor.living_room_mi_flora_01_moisture
      below: 20
    action:
      service: telegram_bot.send_message
      data:
        message: 'Por favor, riega la orquídea cuanto antes.'
  ```

**Guardamos**. Recomiendo reiniciar nuestro servidor.

Cuando la humedad de nuestra planta se sitúe por debajo de un 20%, se lanzará la notificación y nos llegará a la conversación previamente creada.

![Prueba de notificación en Telegram](/assets/posts/es/notificaciones-de-home-assistant-en-telegram/image06.jpg)

## Conclusión

Gracias a estos sencillos pasos hemos configurado un *bot* de *Telegram* y su integración con *Home Assistant* para recibir notificaciones de todo lo que se nos ocurra. Incluso podemos crear acciones con botones dentro del propio *bot* para controlar varios aspectos de nuestra casa inteligente.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
