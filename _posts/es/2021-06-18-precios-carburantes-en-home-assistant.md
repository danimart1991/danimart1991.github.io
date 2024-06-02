---
title: "Precios Carburantes en Home Assistant"
header:
  image: /assets/posts/es/precios-carburantes-en-home-assistant/header.png
categories:
  - Tutorial
  - Home Assistant
tags:
  - Diesel
  - Gasolina
  - Home Assistant
  - Sensores
  - Tutorial
lang: es
ref: 41
permalink: /es/precios-carburantes-en-home-assistant/
---

[**_Home-Assistant_**](https://www.home-assistant.io/) ofrece multitud de integraciones para distintos dispositivos y servicios, pero muchas veces, no son suficientes. Una manera de crear nuevas integraciones es la de desarrollar una personalizada, pero eso requiere conocimientos de la arquitectura de _Home Assistant_, de programación en [_Python_](https://www.python.org/)... Hoy vamos a ver cómo crear una que ofrezca sensores para los precios de los distintos carburantes (diésel, gasolina...) en nuestra gasolinera de confianza sin necesidad de todo eso.

Quiero agradecer antes de nada a [_Domology_](https://domology.es/) y a su grupo de [_Telegram_](https://t.me/Domology), la idea para la realización de este post y el gran trabajo de varios de sus usuarios que me han servido de ayuda para conseguir el objetivo.

## Requisitos

- Saber modificar los archivos _YAML_ de _Home Assistant_ y más o menos manejarnos con ellos. En el blog hay disponibles distintos artículos para familiarizarse con estos conceptos.

## Primeros pasos

Vamos a usar el servicio _REST_ de consulta de **Precios de Carburantes** que ofrece el **Ministerio de Industria, Comercio y Turismo del Gobierno de España**. La documentación se encuentra disponible en esta _URL_: *https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/help*

Es importante conocer el `IDEESS` (Id Estación de Servicio) de la gasolinera que vamos a consultar, así como el `IDMunicipio` (Id Municipio) donde se encuentra, ya que, aunque vamos a realizar una consulta cada 30 minutos, cuanto más centremos la llamada, menos coste tendrá. Si vamos a consultar varias gasolineras, o distintos carburantes, y no configuramos esto bien, pueden realizarse llamadas de más.

En la siguiente _URL_ pueden consultarse el listado de Provincias disponibles, busca la que te interese y apunta su información: https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/Listados/Provincias/

```json
// Ejemplo https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/Listados/Provincias/

{
  "IDPovincia": "02",
  "IDCCAA": "07",
  "Provincia": "ALBACETE",
  "CCAA": "Castilla la Mancha"
}
```

Una vez la tengas, haz la siguiente llamada sustituyendo `{IDPROVINCIA}` por el Id de la provincia que has apuntado antes: *https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/Listados/MunicipiosPorProvincia/{IDPROVINCIA}*

```json
// Ejemplo https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/Listados/MunicipiosPorProvincia/02

{
  "IDMunicipio": "52",
  "IDProvincia": "02",
  "IDCCAA": "07",
  "Municipio": "Abengibre",
  "Provincia": "ALBACETE",
  "CCAA": "Castilla la Mancha"
}
```

Del mismo modo, del listado que se muestra, busca el municipio que te interese y apunta su información.

Por último, realizamos la llamada *https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/{IDMUNICIPIO}*, cambiando `{IDMUNICIPIO}` por el guardado previamente. En el listado que se muestra, apuntamos los **_IDEESS_** de la/s gasolinera/s que nos interese. También se pueden ver los tipos de carburantes disponibles para crear los sensores con el nombre de _Precio + Tipo_.

```json
// Ejemplo https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/52

{
  "Fecha": "18/06/2021 10:59:49",
  "ListaEESSPrecio": [
    {
      "C.P.": "02250",
      "Dirección": "AVENIDA CASTILLA LA MANCHA, 26",
      "Horario": "L-D: 07:00-22:00",
      "Latitud": "39,211417",
      "Localidad": "ABENGIBRE",
      "Longitud (WGS84)": "-1,539167",
      "Margen": "D",
      "Municipio": "Abengibre",
      "Precio Biodiesel": "",
      "Precio Bioetanol": "",
      "Precio Gas Natural Comprimido": "",
      "Precio Gas Natural Licuado": "",
      "Precio Gases licuados del petróleo": "",
      "Precio Gasoleo A": "1,229",
      "Precio Gasoleo B": "0,729",
      "Precio Gasoleo Premium": "",
      "Precio Gasolina 95 E10": "",
      "Precio Gasolina 95 E5": "1,359",
      "Precio Gasolina 95 E5 Premium": "",
      "Precio Gasolina 98 E10": "",
      "Precio Gasolina 98 E5": "",
      "Precio Hidrogeno": "",
      "Provincia": "ALBACETE",
      "Remisión": "dm",
      "Rótulo": "Nº 10.935",
      "Tipo Venta": "P",
      "% BioEtanol": "0,0",
      "% Éster metílico": "0,0",
      "IDEESS": "4375",
      "IDMunicipio": "52",
      "IDProvincia": "02",
      "IDCCAA": "07"
    }
  ],
  "Nota": "Archivo de todos los productos en todas las estaciones de servicio. La actualización de precios se realiza cada media hora, con los precios en vigor en ese momento.",
  "ResultadoConsulta": "OK"
}
```

> Nota: Si al usar las _URLs_ anteriores en un navegador, no se ve correctamente, se pueden usar Webs como por ejemplo: *https://codebeautify.org/jsonviewer* poniendo las _URLs_ en el cuadro de texto que se abre al pulsar **_Load Url_**.

## Creando Sensores

Solo queda crear los sensores con los precios del carburante. Simplemente, modificamos el archivo `configuration.yaml` e incluimos el siguiente código.

```yaml
rest:
  - scan_interval: 1800
    resource: https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/{IDMUNICIPIO}
    sensor:
      - name: "Precio Diésel - Gasolinera 1"
        value_template: >-
          {% raw %}{% set station = value_json['ListaEESSPrecio'] | selectattr('IDEESS', 'match', '{IDEESS}') | first %}
          {{ station['Precio Gasoleo A'] | replace(",",".") }}{% endraw %}
        unit_of_measurement: "€/L"
```

Necesario sustituir `{IDMUNICIPIO}` e `{IDEESS}` por los valores que hemos apuntado previamente.

Esto creará un sensor con el precio de Gasóleo A (Diésel) en nuestro sistema.

![Sensor Diésel Gasolinera 1](/assets/posts/es/precios-carburantes-en-home-assistant/sensor-carburante.png)

Este código se puede mejorar y cambiar para obtener más información o para personalizarlo según nos interese:

- Para obtener otro tipo de carburante, basta con cambiar `Precio Gasoleo A` por uno de los siguientes:

  - Precio Biodiesel
  - Precio Bioetanol
  - Precio Gas Natural Comprimido
  - Precio Gas Natural Licuado
  - Precio Gases licuados del petróleo
  - Precio Gasoleo A
  - Precio Gasoleo B
  - Precio Gasoleo Premium
  - Precio Gasolina 95 E10
  - Precio Gasolina 95 E5
  - Precio Gasolina 95 E5 Premium
  - Precio Gasolina 98 E10
  - Precio Gasolina 98 E5
  - Precio Hidrogeno

- Se puede duplicar el bloque de código `sensor` para crear más sensores para distintos tipos de carburante o distintas gasolineras. Ejemplo:

  ```yaml
  rest:
    - scan_interval: 1800
      resource: https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/51
      sensor:
        - name: "Precio Diésel - Gasolinera 1"
          value_template: >-
            {% raw %}{% set station = value_json['ListaEESSPrecio'] | selectattr('IDEESS', 'match', '104') | first %}
            {{ station['Precio Gasoleo A'] | replace(",",".") }}{% endraw %}
          unit_of_measurement: "€/L"
        - name: "Precio Gasolina 98 - Gasolinera 2"
          value_template: >-
            {% raw %}{% set station = value_json['ListaEESSPrecio'] | selectattr('IDEESS', 'match', '105') | first %}
            {{ station['Precio Gasolina 98 E10'] | replace(",",".") }}{% endraw %}
          unit_of_measurement: "€/L"
  ```

- Si por un casual, las gasolineras no se encontrasen en el mismo municipio, se puede solucionar de dos maneras:

  - Si ambas gasolineras se encuentran en la misma provincia, se puede cambiar la URL de `resource` por: `https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroProvincia/{IDPROVINCIA}`.

  - O se pueden crear dos integraciones rest distintas:

    ```yaml
    rest:
      - scan_interval: 1800
        resource: https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/51
        sensor:
          - name: "Precio Diésel - Gasolinera 1"
            value_template: >-
              {% raw %}{% set station = value_json['ListaEESSPrecio'] | selectattr('IDEESS', 'match', '105') | first %}
              {{ station['Precio Gasoleo A'] | replace(",",".") }}{% endraw %}
            unit_of_measurement: "€/L"
      - scan_interval: 1800
        resource: https://sedeaplicaciones.minetur.gob.es/ServiciosRESTCarburantes/PreciosCarburantes/EstacionesTerrestres/FiltroMunicipio/52
        sensor:
          - name: "Precio Gasolina 98 - Gasolinera 2"
            value_template: >-
              {% raw %}{% set station = value_json['ListaEESSPrecio'] | selectattr('IDEESS', 'match', '4375') | first %}
              {{ station['Precio Gasolina 98 E10'] | replace(",",".") }}{% endraw %}
            unit_of_measurement: "€/L"
    ```

## Conclusión

De una manera sencilla, y sin necesidad de conocimientos avanzados de programación hemos creado sensores que nos ofrecen la información del precio de los carburantes en las gasolineras de España. Este tutorial puede extenderse a otros servicios que ofrezcan una interfaz REST para así obtener distinta información que puede ser de utilidad en nuestro Home Assistant.

Este y otros artículos complementan la documentación del [**repositorio de _GitHub_**](https://github.com/danimart1991/home-assistant-config) donde se encuentra disponible toda la configuración de mi casa.
