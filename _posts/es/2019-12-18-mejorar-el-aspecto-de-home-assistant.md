---
title: "Mejorar el aspecto de Home Assistant"
header:
  image: /assets/posts/es/mejorar-el-aspecto-de-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 26
permalink: /es/mejorar-el-aspecto-de-home-assistant/
---

[*Home Assistant*](https://www.home-assistant.io/) tiene una interfaz *Web* basada en [*Material Design* de *Google*](https://material.io/). Si bien es bastante agradable a la vista, para aquellos que quieran cambiar un poco el aspecto, *Home Assistant* nos brinda opciones para cambiar su estilo.

> Available in English [here](https://danielmartingonzalez.com/improve-the-look-and-feel-of-home-assistant)

## Funcionamiento de los temas

Dentro de la configuración de *Home Assistant,* modificando el archivo `configuration.yaml`, existe la integración [`frontend`](https://www.home-assistant.io/integrations/frontend/); y dentro de ella, el nodo [`themes`](https://www.home-assistant.io/integrations/frontend/#defining-themes), con el que se pueden crear diferentes temas que se podrán usar en la interfaz simplemente seleccionándolos en nuestro perfil de usuario.

Estos ***themes*** basan su configuración en modificar variables *CSS*, esto es, variables de estilo que se usan en la interfaz *Web*. Así, por ejemplo, se puede crear un *Theme* llamado ***Happy***, que cambie la variable del color primario por el color rosa.

```yaml
frontend:
  themes:
    happy:
      primary-color: pink
```

Para usar el tema recién creado, basta con ir al perfil de usuario de *Home Assistant* y seleccionar el tema.

![Selección de temas en Home Assistant](/assets/posts/es/mejorar-el-aspecto-de-home-assistant/image01.jpg)

## Temas de la comunidad

Como empezar a crear un tema desde cero, buscando las variables *CSS* y probando hasta dar con algo bonito puede ser muy complicado, la comunidad crea y comparte temas para que cualquiera pueda usarlos. Basta con preparar la configuración de temas de *Home Assistant* y después incluir los temas como archivos independientes.

1. En el archivo `configuration.yaml` pon las siguientes líneas sustituyendo las líneas del theme *Happy* que se ha probado anteriormente.

    ```yaml
    frontend:
      themes: !include_dir_merge_named themes/
    ```

2. En el mismo directorio donde se encuentra el archivo `configuration.yaml`, crea una carpeta llamada `themes`.

    ![Relación de directorios de Temas](/assets/posts/es/mejorar-el-aspecto-de-home-assistant/image02.jpg)

3. Dentro se ha de crear un archivo `NOMBRETEMA.yaml` por cada tema que se quiera incluir, de este modo, para el ejemplo anterior, crear un archivo `happy.yaml` con el siguiente contenido:

    ```yaml
    happy:
      primary-color: pink
    ```

4. Guarda, reinicia el servidor y comprueba que puedes seguir seleccionando el tema *Happy*.

5. Desde el [foro oficial de la comunidad](https://community.home-assistant.io/), en la sección de [Themes]((https://community.home-assistant.io/c/projects/themes)), se pueden **descargar y compartir temas** hechos por la comunidad. Simplemente han de seguir la misma estructura.

    Por ejemplo, se puede copiar/descargar el tema [Clear](https://community.home-assistant.io/t/clear-theme/100464). Para ello, solo se ha de crear el archivo `clear.yaml` dentro de la carpeta `themes` e incluir dentro todo el contenido indicado por el autor en el hilo del foro:

    ```yaml
    clear:
      # Background image
      lovelace-background: 'center / cover no-repeat url("/local/day.jpg") fixed'

      # Colors
      text-color: '#636B75'
      text-medium-color: '#8c96a5'
      text-light-color: '#BAC0C6'
      accent-color: '#00a1ff'
      background-color: '#F7F8F9'
      background-color-2: '#F4F5F6'
      background-card-color: 'rgba(255,255,255,1.0)'
      border-color: '#E8E8E8'

      # ...
    ```

    > Algunos temas incluyen **imágenes**, normalmente con la dirección `/local/.../IMAGEN.jpg`. Estas imágenes suelen acompañar al tema y se deben descargar y guardar en la carpeta `www` que también debe estar en el mismo directorio que el archivo `configuration.yaml` y la carpeta `themes`. Al reiniciar, *Home Assistant* moverá los archivos siguiendo el mismo esquema de directorios a su carpeta `local`.

6. El último paso consiste en **guardar, reiniciar y probar los nuevos temas**.

    ![Prueba de Tema Clear](/assets/posts/es/mejorar-el-aspecto-de-home-assistant/image03.jpg)

## Cambiar de tema automáticamente

Una de las funciones más de moda estos días en las aplicaciones es la de cambiar su estilo entre uno **claro y oscuro** dependiendo si es de día o de noche respectivamente.

Esta funcionalidad es fácilmente reproducible en *Home Assistant* usando una **automatización**. Solo necesitas tener al menos dos temas configurados (el tema por defecto puede usarse) y la entidad [`sun`](https://www.home-assistant.io/integrations/sun/) configurada. Se puede hacer de dos formas:

La primera opción, es visual. Desde el menú `Configuración -> Automatizaciones` crea una nueva automatización.

1. Introduce un nombre para la automatización.

2. Introduce **3 desencadenantes**, uno cuando *Home Assistant* se inicie, y otros dos para los eventos amanecer y anochecer del Sol.

    ![Desencadenantes automatización](/assets/posts/es/mejorar-el-aspecto-de-home-assistant/image04.jpg)

3. Introduce una acción que llame al servicio de cambio de tema (`frontend.set_theme`), pero dependiendo del estado de la entidad `sun`, asigne un tema u otro. Indica los temas que quieres usar.

    ![Acción automatización](/assets/posts/es/mejorar-el-aspecto-de-home-assistant/image05.jpg)

4. Guarda y reinicia el servidor. Ya tendrás el cambio automático de temas funcional.

Otra opción es programar la automatización mediante el siguiente código `YAML`:

```yaml
- id: change_theme_on_sun_horizon
  alias: Change theme ON sun horizon
  trigger:
    - platform: homeassistant
      event: start
    - platform: state
      entity_id: sun.sun
      to: "above_horizon"
    - platform: state
      entity_id: sun.sun
      to: "below_horizon"
  action:
    - service: frontend.set_theme
      data_template:
        name: >
          {% raw %}{% if states.sun.sun.state == "above_horizon" %}
            clear
          {% else %}
            clear-dark
          {% endif %}{% endraw %}
```

Ambas generan el mismo resultado.

## Conclusión

Con este breve artículo hemos descubierto como dar un lavado de cara a la interfaz gráfica de *Home Assistant* y a como automatizar los cambios de temas. Ahora solo queda probar hasta dar con el tema que mejor defina tu hogar.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
