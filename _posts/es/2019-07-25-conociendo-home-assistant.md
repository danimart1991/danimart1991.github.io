---
title: "Conociendo Home Assistant"
header:
  image: /assets/posts/es/conociendo-home-assistant/header.jpg
tags: homeassistant hassio domotica
lang: es
ref: 13
permalink: /es/conociendo-home-assistant/
last_modified_at: 2020-08-31
---

Una vez [creado nuestro servidor **Home Assistant**](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/), vamos a ver cómo funciona su interfaz web y como realizar las configuraciones básicas para empezar a trabajar y disfrutar de nuestra casa inteligente.

Para acceder a la interfaz web, tal y como vimos en el [artículo anterior](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/), basta con acceder a `http://homeassistant.local:8123` si tu router dispone de *mDNS*, o `http://xxx.xxx.xxx.xxx:8123` en caso contrario, sustituyendo las `x` por la *IP* que configuraste previamente en la configuración de red, y a continuación iniciando sesión con la cuenta creada durante la instalación.

## *Lovelace*

[Home Assistant](https://www.home-assistant.io/), aparte de servidor web, expone una web app para que podamos visualizar todas las entidades y automatizaciones creadas de una manera muy simple. A toda esta parte visual se la denomina [**Lovelace**](https://www.home-assistant.io/lovelace/).

Por defecto **Lovelace** viene configurado para "autodescubrir" configuraciones y automatizaciones, eso significa qué si integramos algún dispositivo por medio del asistente de integraciones, o como veremos más adelante mediante el archivo de configuración, automáticamente se nos mostrarán tarjetas e información en la pantalla principal (*Resumen*).

![Home Assistant Lovelace](/assets/posts/es/conociendo-home-assistant/lovelace-dashboard.png)

A parte, y desde el menú de la izquierda, dispondremos por defecto de las siguientes características:

- **Mapa**: Un mapa a pantalla completa donde tendremos disponibles todos los dispositivos o personas que hayamos marcado para rastrear, así como distintas zonas que configuremos. Por ejemplo, una zona podría ser nuestra casa, y otra la oficina de trabajo. De esta manera, en Home Assistant nuestra pareja podría saber si estamos llegando a casa o estamos en un atasco.

- **Registro**: Página para ver todos los cambios que han ocurrido en nuestras entidades a lo largo del tiempo a modo de log. Se puede filtrar para tener una visión más clara.

- **Historial**: Página parecida a la pestaña Registro pero muestra los cambios de manera más gráfica en el tiempo.

- **Supervisor**: Si hemos instalado *HA (Home Assistant)* con Supervisor (nuestro caso), desde esta pestaña tendremos disponibles todas las opciones que brinda, tales como Copias de Seguridad (*Snapshots*), instalación y gestión de *Add-ons*, y *Log* del sistema entre otros.

- **Configuración**: Desde aquí realizaremos toda la configuración de *Home Assistant* de una manera más gráfica. Tanto usuarios, como integraciones, áreas, personalización de entidades...

    Para agregar una nueva integración basta con acceder a integraciones desde este menú.

    > Los usuarios más avanzados suelen obviar en gran parte esta pestaña y tratan de configurar todo mediante código. En post futuros veremos las razones.

- **Herramientas para desarrolladores**: Es la parte más avanzada de este menú. Contiene registros y opciones para cambiar los distintos servicios, entidades, estados de las entidades,... así como para crear plantillas y la propia información de *Home Assistant* como versión actual, *log*, e información del sistema.

En el caso de no gustarnos *Lovelace*, hay maneras de cambiar los temas, añadir tarjetas y componentes personalizados para adaptarlo a nuestras necesidades. Y si nada de esto nos convence, hay alternativas de *dashboard* como el [**Add-on Home Panel**](https://github.com/hassio-addons/addon-home-panel).

## Instalando nuestro primer *Add-on*

Para romper el hielo con *Home Assistant*, no hay nada mejor que comenzar a instalar *Add-ons*. Estos *Add-ons* son mejoras que se aplican a nuestro servidor web para dotarles de nuevas características. Para acceder a ellos basta con acceder a la pestaña *Supervisor* y a continuación entrar en la opción *Add-on Store*.

![Add-on Store](/assets/posts/es/conociendo-home-assistant/hassio-addon-store.png)

En esta página dispondremos de los *Add-ons* recogidos de distintos repositorios. Por el momento, del repositorio oficial de *Add-ons* y del repositorio de *Add-ons* verificados de la comunidad. Se pueden añadir tantos repositorios como uno quiera, incluso repositorios propios de *Add-ons* que estemos desarrollando.

A modo de prueba y de paso instalar un *Add-on* bastante útil. Selecciona de la lista el *Add-on* [**Check Home Assistant configuration**](https://github.com/home-assistant/hassio-addons/tree/master/check_config).

![Check Home Assistant Configuration](/assets/posts/es/conociendo-home-assistant/official-addons.png)

Cuando se libera una nueva versión de *Home Assistant*, suele venir a veces con *Breaking Changes*, es decir, con cambios que pueden estropear nuestra configuración y hacer que algunas cosas dejen de funcionar o incluso que todo nuestro sistema colapse y no inicie. El *Add-on* [**Check Home Assistant configuration**](https://github.com/home-assistant/hassio-addons/tree/master/check_config) se encarga de revisar toda nuestra configuración, instalar la nueva versión en un contenedor a parte y probar que no hay ningún problema. No nos asegura al 100% que todo esté perfecto, pero al menos nos da una idea de que podemos instalar e iniciar el servidor una vez actualicemos a la nueva versión.

Para instalar, tan simple como hacer clic en **INSTALL**, y una vez instalado, y tal y como indican en la documentación y para este *Add-on*, hacemos clic en **START**.

![Instalación Check Home Assistant Configuration](/assets/posts/es/conociendo-home-assistant/check-home-assistant-configuration-addon.png)

Tardará unos minutos. Pero cuando acabe, abajo del todo, en el **Log**, tendremos el registro de lo que ha hecho el *Add-on*, y de si nuestro sistema está preparado para actualizar. A partir de este momento, solo debemos dar al botón *START* cada vez que queramos probar que pasaría si actualizáramos *Home Assistant*.

![Check Home Assistant Configuration Log](/assets/posts/es/conociendo-home-assistant/check-home-assistant-configuration-log.png)

> El *Add-on* se parará automáticamente al terminar. Por supuesto, otros *Add-ons* no funcionan de este modo, tienen una funcionalidad permanente e incluso podremos iniciarlos con el inicio de *Home Assistant* de tal manera que siempre estén disponibles.

## Creando nuestra primera automatización

Una de las características más útiles de *Home Assistant* son las [**automatizaciones**](https://www.home-assistant.io/getting-started/automation/). De nuevo, aunque se pueden crear desde el *Dashboard*, tal y como veremos ahora; muchos usuarios tienden a realizar estas automatizaciones mediante código [*yaml*](https://yaml.org/) y *scripts* [*python*](https://www.python.org/) para facilitar su reutilización y copia de seguridad.

Para probar cómo funcionan las automatizaciones, dirígete a **Configuración - Automatizaciones**, y haz clic en el botón para crear una nueva.

![Home Assistant Automatizaciones](/assets/posts/es/conociendo-home-assistant/automatizacion.png)

Pon los siguientes parámetros:

- **Nombre**: HA Iniciado.
- [**Desencadenantes**](https://www.home-assistant.io/docs/automation/trigger/):
  - **Tipo**: Home Assistant
  - **Evento**: Arranque
- [**Condiciones**](https://www.home-assistant.io/docs/automation/condition/): Salta esta parte.
- [**Acciones**](https://www.home-assistant.io/docs/automation/action/):
  - **Tipo**: Llamar Servicio
  - **Servicio**: `persistent_notification.create`
  - **Datos Servicio**:

    ```yaml
    title: "Home Assistant"
    message: "Home Assistant iniciado."
    ```

Guardamos.

![Home Assistant Automatización](/assets/posts/es/conociendo-home-assistant/nueva-automatizacion.png)

La **automatización** creada dispara una notificación para todos los usuarios en el *Dashboard* cuando *Home Assistant* se inicia. Para probarlo, accede a la pestaña **Configuración -> General** y abajo del todo haz clic en **Reiniciar**. Tras esperar unos minutos, *Home Assistant* se habrá reiniciado. Accede de nuevo a la página principal (puedes hacer clic en la pestaña **Resumen**), y veras que abajo a la izquierda aparece una notificación.

![Home Assistant nueva notificación](/assets/posts/es/conociendo-home-assistant/nueva-notificacion.png)

Al hacer clic se mostrará la **notificación** que acaba de mandarse con nuestra automatización.

![Home Assistant mostrar notificación](/assets/posts/es/conociendo-home-assistant/notificacion.png)

## Conclusión

Ahora ya sabes lo básico para empezar a tocar *Home Assistant*. Mi recomendación es que empieces a tocar, crear integraciones, configuraciones, automatizaciones y descubras todo el potencial que *Home Assistant* esconde. En pocos minutos estarás deseoso de hacer toda tu casa inteligente.

Este y otros artículos complementan la documentación del [**repositorio de *GitHub***](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
