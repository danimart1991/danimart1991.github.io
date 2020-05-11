---
title: "Protege tu información privada con secrets.yaml"
header:
  image: /assets/posts/es/protege-tu-informacion-privada-con-secrets-yaml/header.jpg
tags: homeassistant hassio domotica
lang: es
ref: 17
permalink: /es/protege-tu-informacion-privada-con-secrets-yaml/
---

Cuando trabajamos con los archivos de [*Home Assistant*](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/), tendemos a introducir claves y datos privados que en malas manos pueden convertirse en **un problema muy peligroso y serio**.

Si además eres como yo, que tengo pública [toda mi configuración](https://github.com/danimart1991/home-assistant-config), o asiduamente mandas trozos a otras personas para ayudarlas o enseñarlas parte de esa configuración, te habrás visto borrando antes toda tu información privada; con la preocupación de olvidar borrar algún dato importante.

## `secrets.yaml`

*Home Assistant* ofrece como solución un archivo especial llamado `secrets.yaml` que se encuentra en la raíz de nuestra configuración (en la misma carpeta que `configuration.yaml`). En caso de no estar creado, basta con crear un nuevo archivo con este nombre.

![Directorio config](/assets/posts/es/protege-tu-informacion-privada-con-secrets-yaml/image01.jpg)

Este archivo tiene una estructura muy sencilla. Es un **diccionario de claves** que luego se usarán en otros ficheros de la configuración. De esta manera, tendremos todas las claves organizadas y centralizadas en un único archivo.

En los demás ficheros bastará con usar la variable recién creada mediante el uso de `!secret VARIABLE`.

## Ejemplo

Voy a poner un ejemplo sencillo pero bastante claro usando una de las configuraciones vistas en [un artículo anterior](/es/configura-home-assistant-editando-sus-archivos/).

El archivo `configuration.yaml` tiene el siguiente código:

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: 123456789
```

Se puede ver que el nodo `api_key` tiene una clave privada que ninguna otra persona debería ver. Vamos a ocultarla.

En el archivo `secrets.yaml`, incluimos en siguiente nodo:

```yaml
weather_openweathermap_api_key: 123456789
```

Para usarlo, basta con cambiar el nodo de `configuration.yaml` como sigue:

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: !secret weather_openweathermap_api_key
```

Algo tan sencillo y efectivo como este cambio puede aplicarse a cualquier clave o dato que queramos proteger.

## Conclusión

Con el uso de `secrets.yaml` ya no tendremos miedo a compartir nuestra configuración, y no hará falta una previa revisión, pues todas nuestras claves y datos privados los tendremos localizados en el archivo `secrets.yaml`. Archivo que recomiendo no compartir con ninguna otra persona.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
