---
title: "Imágenes para personas en Home Assistant"
header:
  image: /assets/posts/es/imagenes-para-personas-en-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 28
permalink: /es/imagenes-para-personas-en-home-assistant/
---

[En el artículo anterior se hizo la distinción entre usuario y persona](/es/configuracion-de-home-assistant-en-yaml/). Por defecto se suele crear una persona por cada usuario. Y se usa para [rastrear](https://www.home-assistant.io/integrations/device_tracker/) al usuario y crear automatizaciones para por ejemplo, apagar la calefacción cuando todas las personas están fuera de casa.

Si no has modificado la configuración de [Lovelace](https://www.home-assistant.io/lovelace/), verás un indicador de la persona como insignia ([*badge*](https://www.home-assistant.io/lovelace/views/#badges)) en la parte de arriba de la página principal. Si has configurado un rastreador (*tracker*), tendrás además información del lugar donde se encuentra la persona.

![Dashboard de Home Assistant con Persona como Badge](/assets/posts/es/imagenes-para-personas-en-home-assistant/resumen-persona.jpg)

Sin embargo, este icono es siempre el mismo para todas las personas y suele verse feo y difícil de distinguir. Lo bueno, es que puede cambiarse por una imagen para que sea fácilmente identificable. No hay una manera obvia de asociar una imagen a una persona. Pero hay un pequeño truco para poder cambiarlo desde el menú de Personalización o desde `customize.yaml`.

## Personalizando a las personas

![Personalización de manera visual de Persona](/assets/posts/es/imagenes-para-personas-en-home-assistant/personalizacion-persona.jpg)

Desde el menú **Configuración -> Personalización** selecciona la persona a la que quieras asignar un icono o imagen. Los atributos que han de cambiarse son [`icon`](https://www.home-assistant.io/docs/configuration/customizing-devices/#icon) o [`entity_picture`](https://www.home-assistant.io/docs/configuration/customizing-devices/#entity_picture). Esta última no aparecerá en el menú por defecto, es necesario seleccionar `Otro` y poner el nombre del atributo y el valor.

En el caso de `icon`, el valor será `mdi:` seguido del icono elegido. Puedes consultar un listado de iconos en páginas como [Material Design Icons](http://materialdesignicons.com/). Quedará algo como `mdi:alien`.

Si se quiere usar una imagen, ha de usarse el atributo `entity_picture`, puede usarse una dirección web o subir una imagen a la carpeta `www` en el mismo directorio donde se encuentra el archivo `configuration.yaml`. Para que *Home Assistant* pueda usar el archivo tendremos cambiar en la dirección `www` por `local` (aunque realmente la imagen se encuentre en `www`).

![Imagen de persona en servidor en carpeta www](/assets/posts/es/imagenes-para-personas-en-home-assistant/configurator-archivo-foto.jpg)

Guarda los cambios y ya tendrás disponible el cambio.

## Personalizar por código

Si se prefiere realizar el cambio mediante código *YAML*. En el archivo `customize.yaml` ([previamente declarado en `configuration.yaml`](https://www.home-assistant.io/docs/configuration/customizing-devices/#manual-customization)) usa una de las siguientes líneas de código.

- Cambiar el icono:

  ```yaml
  person.dani:
    icon: mdi:alien
  ```

- Cambiar la imagen (subiendo previamente la imagen a `www`):

  ```yaml
  person.dani:
    entity_picture: "/local/dani.jpg"
  ```

Reinicia el servidor y listo.

![Dashboard de Home Assistant con Persona con imagen cambiada](/assets/posts/es/imagenes-para-personas-en-home-assistant/resumen-persona-cambiada.jpg)

## Conclusión

Con estos sencillos pasos se ha configurado la entidad persona para darle un aspecto más visual y que sea fácilmente distinguible. Estos pasos pueden utilizarse casi con cualquier entidad para mejorar el aspecto de la interfaz del servidor *Home Assistant*.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
