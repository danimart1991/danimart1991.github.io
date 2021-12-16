---
title: "Migrando a Docker Compose"
header:
  image: /assets/posts/es/migrando-a-docker-compose/header.png
categories:
  - Tutorial
  - Docker
tags:
  - Compose
  - Docker
  - Tutorial
  - Contenedores
lang: es
ref: 45
permalink: /es/migrando-a-docker-compose/
---

Docker Compose es una herramienta pensada para ejecutar varias aplicaciones en contenedores que están relacionados entre si. Estas relaciones se definen en un archivo con formato Compose en YAML.

Todo este sistema brinda opciones que de trabajar solo con Docker, tendríamos que controlar mediante scripts o con una gestión manual de estos contenedores.

Actualmente Docker Compose se encuentra en su versión 2, actuando como plugin de Docker. Mientras que la versión 1 actuaba como un binario autónomo. Para el uso habitual que solemos dar a la herramienta la única diferencia es que ahora se invoca usando el comando `docker compose` en vez de `docker-compose`.

## Funcionamiento



## Ventajas

## Instalación

$ sudo mkdir -p /usr/local/lib/docker/cli-plugins
$ sudo curl -SL https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-linux-x86_64 -o /usr/local/lib/docker/cli-plugins/docker-compose
$ sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
$ docker compose version
Docker Compose version v2.2.2

## Creando tus docker-compose.yml





[Ahora que manejamos más o menos adecuadamente _Docker_](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian/), y que hemos instanciado unos cuantos servicios en nuestro servidor con él, el sistema empieza a llenarse de datos y configuraciones. Tal y como hemos ido viendo, todos estos datos que queremos persistir se vinculan desde el servicio con nuestro _host_ mediante [el uso de volúmenes o _bind mounts_](https://docs.docker.com/storage/). Ante este aumento mas o menos organizado, se nos plantea el problema de poder perder todo el trabajo realizado hasta la fecha si ocurre una catástrofe en nuestro servidor.

**Hoy vamos a ver la forma de poder almacenar todos estos datos en un almacenamiento secundario, o incluso en un servicio en la nube y conservar una o varias copias de seguridad para así poder revivir un servicio ante cualquier fallo crítico.**

## Duplicati

Entre todos los servicios que he probado para crear las copias de seguridad, me quedo con [**_Duplicati_**](https://www.duplicati.com/) porque **dispone de todos los requisitos que necesito y alguno más**:

- Soporta encriptación de archivos y carpetas mediante cifrado _AES-256_.
- Dispone tanto de interfaz _Web_ como línea de comandos para la gestión total del servicio.
- Permite alojar las copias de seguridad de manera muy simple en remoto (esto incluye protocolos como _FTP_, _WebDAV_, _SSH_... o nubes como _Microsoft OneDrive_, _Amazon S3_, _Google Drive_, etc.).
- Copias de seguridad incrementales, con lo que conseguimos ahorrar una cantidad enorme de espacio guardando solo los cambios realizados desde la carga anterior.
- Planificador para realizar las copias de seguridad cuando nos interese.
- Utiliza la tecnología de deduplicación. _Duplicati_ analiza el contenido de los archivos y almacena bloques de datos. Debido a eso, _Duplicati_ encontrará archivos duplicados y contenido similar y los almacenará solo una vez en la copia de seguridad. Como _Duplicati_ analiza el contenido de los archivos, puede manejar situaciones muy bien si los archivos y carpetas se mueven o se les cambia el nombre. Como el contenido no cambia, la próxima copia de seguridad será pequeña.
- Copias de seguridad inteligentes: Guarda copias de seguridad gradualmente en el tiempo, y desecha las antiguas. Guardando una copia de seguridad de los últimos 7 días, otra copia cada una de las últimas 4 semanas y una última copia cada uno de los 12 meses. Pero también podemos marcar otro tipo de retención si nos interesa más.
- Permite actualizaciones automáticas. Aunque en mi caso, [usaré _Ouroboros_](https://www.danielmartingonzalez.com/es/actualizacion-automatica-de-contenedores/) para mantener actualizado el contenedor _Docker_ en el que vamos a instanciarlo.

Por contra, presenta una serie de inconvenientes, que por ahora no han sido "solucionados".

- No posee un sistema de notificaciones potente. Apenas se pueden mandar _Emails_ o mensajes _HTTP_ ante eventos que ocurran al realizar las copias de seguridad.
- Si perdemos la clave de cifrado, todas las copias de seguridad que usen esta clave quedarán inservibles.
- Si por cualquier motivo la base de datos de _Duplicati_ se corrompe, es muy posible que las copias de seguridad queden inservibles en esa instancia. Para solucionarlo, habría que reinstanciar _Duplicati_ con una instalación nueva y restaurar una copia de seguridad de su configuración.
- Las copias de seguridad solo pueden ser gestionadas, realizadas o restauradas con _Duplicati_. Usa un sistema de copias de seguridad propio y no pueden ser abiertos con otra aplicación o gestor de archivos.

## Preparación

A la hora de hacer copias de seguridad, **no todos los datos deben o no queremos que sean conservados**. Es algo que se debe pensar detenidamente y analizar con el contexto de cada servicio. Por ejemplo, yo soy un poco reacio a guardar los registros (_logs_), ya que personalmente, no es algo que para mí tenga interés conservar. Pero igual puede ser importante para otra persona para, por ejemplo, detectar posibles errores o mejoras.

Aunque _Duplicati_ permite una gran variedad de filtros, selección de carpetas o incluso ignorar archivos y carpetas, a mi parecer, **lo ideal es tener dividida la información de los contenedores _Docker_ en dos o tres carpetas según la criticidad de sus datos**. Eso hace que no tenga que modificar la configuración de las copias de seguridad en _Duplicati_ si añado o quito un contenedor y, de esta manera tengo localizada la información por su criticidad. Así, por ejemplo, podemos organizar las carpetas de esta forma:

```yaml
/
├ docker
│ ├ etc # Carpeta donde guardo los volúmenes importantes que quiero conservar
│ │ ├ contenedor1_config
│ │ ├ contenedor1_data
│ │ ├ contenedor2_config
│ │ └ ...
│ └ tmp # Carpeta donde guardo los volúmenes con logs o archivos temporales
│   ├ contenedor1_log
│   ├ contenedor2_log
│   ├ contenedor2_cache
│   └ ...
└ ...
```

Esto no es más que una opción, pero puedes ordenar las carpetas de volúmenes como más te interese o usar los filtros que trae _Duplicati_ si te es más útil para tu sistema.

Los propios servicios, muchas veces, permiten configurar las carpetas donde se guardan sus datos y así poder separar lo que queremos guardar de lo que no es importante. Una vez configurado el servicio, es necesario reconfigurar los volúmenes asociados.

## Instalación

Como en otras ocasiones, **vamos a instanciar el servicio usando _Docker_**. El [contenedor de _Duplicati_](https://hub.docker.com/r/duplicati/duplicati) no tiene mucha complejidad, solo debemos ejecutar el siguiente comando en nuestro terminal cambiando un par de parámetros.

```bash
$ docker run -d --name=duplicati --hostname=duplicati -e TZ=Europe/Madrid --net=bridge -p 8200:8200 -v /docker/etc/duplicati_data:/data -v /docker/backups:/backups -v /docker/etc:/source --restart always duplicati/duplicati
```

> También dispones de un ejemplo para _Docker Compose_ [aquí](https://github.com/danimart1991/docker-compose-files/tree/master/duplicati).

Como puedes ver, voy a utilizar la carpeta `/docker/backups` del _host_ para guardar las copias de seguridad locales y la carpeta `/docker/etc` como origen de datos para realizar las copias de seguridad. Como es común en _Docker_, puedes vincular todas las carpetas que creas apropiadas con el contenedor para realizar las copias de seguridad a tu gusto.

> Nota: También puedes observar como incluyo la propia carpeta de configuración de _Duplicati_ entre los archivos que voy a guardar metiéndola dentro de la carpeta de origen de datos con `-v /docker/etc/duplicati_data:/data`.

Una vez instanciado el servicio de _Duplicati_. Puedes acceder a su gestor _Web_ desde `http://IPDETUSERVIDOR:8200`.

## Configurando Copias de Seguridad

Realizados los preparativos e instalado _Duplicati_, **ya podemos crear nuestro sistema de copias de seguridad automatizadas**.

Una vez dentro de la página de gestión de _Duplicati_, hacemos click en _"Añadir copia de seguridad"_ y _"Configurar nueva copia de seguridad"_.

[![Configurar nueva copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/nueva-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/nueva-copia-seguridad.png)

En el primer paso, indicamos un nombre y descripción que sean razonables y activa el cifrado _AES-256_, esto protegerá los datos copiados. Genera o escribe una frase de seguridad y apúntala en un lugar secreto, **ES MUY IMPORTANTE**.

[![Configuración general copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-general-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-general-copia-seguridad.png)

En el segundo paso, marcamos cual queremos sea el destino de la copia de seguridad. En este ejemplo, queremos que las copias acaben en una nube personal con _Microsoft OneDrive_, para ello solo tendremos que iniciar sesión en el servicio e indicar una carpeta de destino.

[![Configuración destino copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-destino-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-destino-copia-seguridad.png)

En el tercer paso, configuraremos los datos de origen, lo haremos al vincular la carpeta del _host_ donde guardamos los datos sobre los que queremos realizar copias de seguridad con la carpeta `source` dentro del contenedor de _Duplicati_, seleccionamos esa carpeta como origen. En su interior, podremos ver las carpetas sobre las que se hará la copia.

Podemos realizar otras configuraciones si así se desea, e incluso aplicar filtros o exclusiones.

[![Configuración origen copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-origen-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-origen-copia-seguridad.png)

En el cuarto paso vamos a configurar un horario en el que realizar las copias de seguridad. En este caso, quiero que la copia se ejecute todos los días a las 05:00.

[![Configuración horario copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-horario-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-horario-copia-seguridad.png)

En el quinto paso, se deciden otras opciones como puede ser el tamaño de los volúmenes (bloques en los que se dividirá la copia de seguridad), o qué tipo de conservación se desea, siendo lo recomendable (aunque no obligado) una retención de copias inteligente. Esta configuración como las otras dependerá del tipo de copia de seguridad que deseemos y de la capacidad del destino.

[![Otras opciones copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-otros-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/configuracion-otros-copia-seguridad.png)

Una vez guardada la configuración, podemos realizar una primera copia de seguridad para comprobar que todo funciona correctamente.

Desde la pantalla inicio, dispondremos de toda la información sobre el número de copias realizadas, espacio ocupado por las mismas, próximas ejecuciones, etc.

[![Detalle copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/detalle-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/detalle-copia-seguridad.png)

## Restaurar Copias de Seguridad

Imaginemos por un momento, que ha ocurrido una catástrofe y queremos recuperar datos de una copia de seguridad. **_Duplicati_ nos permite restaurar copias de seguridad de manera incremental si así se ha configurado y restaurar partes de esa copia como archivos o carpetas por separado**.

Si la catástrofe no es tan grave como para haber destruido _Duplicati_ y su configuración, basta con entrar en el gestor _Web_ de _Duplicati_, hacer click en el menú _"Restaurar"_, y seleccionar la configuración desde la que se quiere recuperar (_Docker Services_ en este artículo). Luego, seleccionamos los datos a recuperar y por último que se desea hacer con esos datos. Por ejemplo, podemos recuperar un archivo por un cambio que ha roto una configuración de algún servicio, a una versión anterior del mismo.

[![Restaurar configuración copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/restaurar-configuración-copia-seguridad.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/restaurar-configuración-copia-seguridad.png)

[![Opciones restauración copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/opciones-restauracion.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/opciones-restauracion.png)

Pero, también puede ocurrir que la catástrofe sea total y la configuración del servidor o de _Duplicati_ haya quedado inservible. Para poder restaurar una copia más "seria" desde un sitio que no hemos configurado previamente en _Duplicati_ al ser una instalación nueva, podremos seleccionar _"Restaurar directamente desde ficheros de copia de seguridad..."_, decirle que la copia está alojada por ejemplo en una nube y desde ahí, restaurar los datos de la misma forma que he comentado en el párrafo anterior.

[![Restauración remota copia de seguridad](/assets/posts/es/copias-de-seguridad-para-con-docker/restauracion-remoto.png)](/assets/posts/es/copias-de-seguridad-para-con-docker/restauracion-remoto.png)

Con este pensamiento en mente, podemos realizar una copia de seguridad de la configuración y base de datos de _Duplicati_, para si ocurre algo grave, poder restaurarlo a posteriori.

## Conclusión

Con lo visto en este post, **deberíamos disponer de varias copias de seguridad en un almacenamiento externo o nube** que nos asegure cierta recuperación ante desastres y pequeños cambios de configuración que rompan el sistema o los servicios instanciados en contenedores _Docker_.

A partir de aquí, y dependiendo de tu configuración en _Duplicati_, **no tendrás que tocar mucho más el servicio**. Si instancias nuevos contenedores, solo has de tener en cuenta los directorios donde creas los volúmenes asociados a los mismos para crear copias de seguridad de los datos que verdaderamente te interesa conservar.

No dudes en preguntar o aportar tu experiencia siguiendo este artículo en los comentarios.

Este y otros artículos complementan la documentación de mis [**repositorios de _GitHub_**](https://github.com/danimart1991) donde se encuentra disponible toda la configuración de mi servidor y la domótica asociada a mi casa.

## Ver también

- [Duplicati - Articles](https://www.duplicati.com/articles/)
- [Duplicati - Documentation](https://duplicati.readthedocs.io/en/latest/)
- [DockerHub - Duplicati Docker Documentation](https://hub.docker.com/r/duplicati/duplicati)
