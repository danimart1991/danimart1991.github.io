---
title: "Docker y Portainer en Debian"
header:
  image: /assets/posts/es/docker-y-portainer-en-debian/header.jpg
categories:
  - Tutorial
  - PC
  - Linux
lang: es
ref: 36
permalink: /es/docker-y-portainer-en-debian/
last_modified_at: 2024-08-16
---

Como ya nos descubrió nuestro compañero [**Joaquín García** en un extenso artículo en **No Country for Geeks**](https://www.nocountryforgeeks.com/contenerizacion-de-aplicaciones-en-docker/), [**_Docker_**](https://www.docker.com/) es una plataforma para ejecutar aplicaciones en contenedores, siendo estos contenedores un método de virtualización que incluye todo lo imaginable para empaquetar de manera sencilla todo un entorno.

![Esquema Docker y contenedores](/assets/posts/es/docker-y-portainer-en-debian/esquema-docker.png)

Dada la flexibilidad con la que pueden desplegarse distintas aplicaciones en un mismo servidor y la independencia de estas respecto a recursos, y sin embargo el poder comunicarlas entre sí, se convierte en **mi opción preferida para construir pieza a pieza todo el ecosistema de mi servidor casero**.

Por su lado [**Portainer**](https://www.portainer.io/) "instalado" como contenedor, nos brinda el poder montar contenedores sobre _Docker_ y configurar todo de manera visual. Incluso trae plantillas con aplicaciones ya preparadas en contenedores para que su configuración sea incluso más fácil.

## Requisitos previos

Aunque en principio la siguiente instalación de _Docker_ y _Portainer_ se puede realizar sobre cualquier sistema operativo basado en _Linux_, todos conocemos que dependiendo de la distribución y versión existen pequeñas diferencias. Por lo que para los siguientes pasos en mi caso se cumplen estos requisitos previos:

- [_Debian 12 64 Bit_ sobre un _Mini-PC_](https://www.danielmartingonzalez.com/es/instalando-debian-desde-cero/). En principio cualquier otra distribución basada en _Debian_ como _Raspbian_ también debería valer.
- [Comando `sudo` instalado](https://www.danielmartingonzalez.com/es/instalando-debian-desde-cero/#bonus-instalar-sudo)
- [Archivo `sources.list` actualizado con colección de paquetes `contrib` y `non-free`](https://www.danielmartingonzalez.com/es/instalando-debian-desde-cero/#bonus-2-actualizar-sourceslist)
- [_IP_ del _Mini-PC_ configurada como estática](https://www.danielmartingonzalez.com/es/ip-estatica-en-debian/)
- Repositorios actualizados con los comandos `sudo apt-get update`, `sudo apt-get upgrade` y `sudo apt-get dist-upgrade`.

## Instalando paquetes y configurando el repositorio

Nos falta un requisito, **añadir el repositorio para poder instalar el paquete _Docker_**.

1. Instala los siguientes paquetes para permitir que `apt` use repositorios seguros sobre [_HTTPS_](https://es.wikipedia.org/wiki/Protocolo_seguro_de_transferencia_de_hipertexto). Ejecutando los siguientes comandos en consola, uno a uno:

   ```bash
   $ sudo apt-get update
   $ sudo apt-get install ca-certificates curl
   ```

2. Añade las claves [_GPG_](https://gnupg.org/) oficiales de _Docker_.

   ```bash   
   $ sudo install -m 0755 -d /etc/apt/keyrings
   $ sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   $ sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```

3. Añade al listado de repositorios el repositorio para obtener _Docker_.

   ```bash
   $ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

   > Si utilizas una distribución derivada de Debian, es posible que no se obtenga la versión correctamente. Tendrás que sustituir `(. /etc/os-release && echo "$VERSION_CODENAME")` por la versión de Debian correspondiente en la distribución, por ejemplo `bookworm`.

4. Por último, actualiza la colección de paquetes.

   ```bash
   $ sudo apt-get update
   ```

## Instalar Docker Engine

Para **instalar la última versión estable de _Docker Engine_** basta con ejecutar:

```bash
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Tras unos segundos podemos comprobar que _Docker_ se ha instalado correctamente preguntando por la versión instalada y ejecutando la imagen `hello-world` de prueba.

```bash
$ docker -v
Docker version 19.03.12, build 48a66213fe

$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:4df8ca8a7e309c256d60d7971ea14c27672fc0d10c5f303856d7bc48f8cc17ff
Status: Downloaded newer image for hello-world:latest
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Para terminar con la instalación de _Docker_, se recomienda **añadir al usuario habitual como administrador de _Docker_** añadiéndolo al grupo `docker`.

```bash
$ sudo adduser TUUSUARIO docker
Añadiendo al usuario 'TUUSUARIO' al grupo docker ...
Hecho.
```

## Portainer

Ahora que _Docker Engine_ está instalado en el servidor, se pueden ejecutar imágenes a través de contenedores. Una de las maneras más sencillas de gestionar estos contenedores, sus volúmenes, imágenes... es con _Portainer_.

### Creación de un volumen

Primero vamos a crear un volumen para los datos de _Portainer_. **Un volumen no es más que un mecanismo de persistencia de datos** para almacenar datos usados y generados por un contenedor _Docker_. De esta manera, aunque borremos, actualicemos, o se rompa el contenedor, los datos de configuración de la aplicación, seguirán a salvo para poder volver a desplegar un nuevo contenedor de la misma imagen sin mayor preocupación.

> Existe cierta discusión sobre si es mejor el uso de [volúmenes](https://docs.docker.com/storage/volumes/) o [montar una carpeta del host](https://docs.docker.com/storage/bind-mounts/) para la persistencia de datos. En la mayoría de los casos que vamos a manejar, es cuestión de gustos. ¡Anímate a probar ambas!

```bash
$ docker volume create portainer_data
portainer_data
```

Aunque más adelante se podrán ver desde _Portainer_, puedes consultar el listado de volúmenes e inspeccionar uno para ver entre otras cosas el directorio donde se encuentran los archivos en el servidor usando los comandos:

```bash
$ docker volume ls
DRIVER      VOLUME NAME
local       portainer_data
$ docker volume inspect portainer_data
[
    {
        "CreatedAt": "2020-07-09T08:16:57+02:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/portainer_data/_data",
        "Name": "portainer_data",
        "Options": {},
        "Scope": "local"
    }
]
```

### Instalar Portainer

Dejando de lado el contenedor `hello_world`, vamos a montar nuestro primer contenedor, esta vez basado en la imagen de _Portainer_. Ejecuta el comando:

```bash
$ docker run -d --name=portainer --hostname=portainer --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data -e TZ='Europe/Madrid' portainer/portainer-ce

Unable to find image 'portainer/portainer-ce'
d1e017099d17: Pull complete
b8084bf83dcf: Pull complete
Digest: sha256:55c...
Status: Downloaded newer image for portainer/portainer-ce:latest
96f1...
```

Como es un comando bastante largo, es recomendable conocer los comandos de `docker` aunque luego se pueda hacer de manera visual desde _Portainer_, voy a explicar cada parte:

- `docker run -d`: Crea un contenedor con la siguiente configuración si no existe y lo arranca.
- `--name=Portainer`: El nombre del contenedor. Es importante pues podemos tener varios contenedores basados en la misma imagen.
- `--hostname=Portainer`: El nombre de la máquina. Opcional, pero viene muy bien configurar este parámetro para ver de un vistazo cuando accedamos por consola este nombre y asociar que esa terminal es de este contenedor. (Gracias a [@Rapejim](https://github.com/rapejim) por el consejo).
- `--network=host`: La red `host` utiliza la dirección IP del servidor, de modo que la red de un contenedor parece ser el propio servidor en lugar de estar separado.
- `--restart=always`: Reinicia el contenedor si se para. Si se para manualmente, solo es reiniciado cuando el contenedor se reinicie manualmente o servicio _Docker_ sea reiniciado.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Vincula una carpeta del servidor (izquierda), con una carpeta del contenedor (derecha). En este caso como _Portainer_ tendrá acceso a los contenedores del servidor, vincula su información con el archivo `docker.sock`.
- `-v portainer_data:/data`: Vincula el volumen, con la carpeta de configuración de _Portainer_ dentro del contenedor.
- `-e TZ='Europe/Madrid'`: Aunque no es necesario, a mí me suele gustar configurar de manera manual la zona horaria para el contenedor.
- `portainer/portainer-ce`: Indica la imagen usada para montar el contenedor. Si no se indica versión cogerá la última versión estable.

### Usando Portainer

A continuación, abre un navegador _Web_ en cualquier dispositivo que esté conectado a la misma red que el servidor y accede a la dirección: `http://IPDETUSERVIDOR:9000/`.

Al acceder por primera vez, se abrirá la pantalla de registro para indicar los datos del que será el usuario administrador de _Portainer_.

![Portainer - Registro administrador](/assets/posts/es/docker-y-portainer-en-debian/portainer-registro.jpg)

Después preguntará por el tipo de instalación. Selecciona _Local_.

![Portainer - Configuración inicial](/assets/posts/es/docker-y-portainer-en-debian/portainer-configuracion-inicial.jpg)

La siguiente pantalla será el _Dashboard_ desde el que se podrán ver los contenedores montados, imágenes descargadas, volúmenes configurados... Los siguientes pasos son aprender un poquito más sobre _Docker_, y empezar a configurar contenedores y posteriormente ver su estado desde _Portainer_.

![Portainer - Lista de contenedores](/assets/posts/es/docker-y-portainer-en-debian/portainer-lista-contenedores.jpg)

## Bonus: Plantillas para Portainer

**Una de las secciones más potentes de _Portainer_ es _App Templates_**. Desde esta pestaña se pueden crear de manera muy sencilla contenedores y _stacks_ ya preparados para desplegar con un par de opciones.

![Portainer - Plantillas](/assets/posts/es/docker-y-portainer-en-debian/portainer-app-templates.jpg)

Basta con seleccionar la aplicación a desplegar, rellenar los campos que se nos muestran y dar a desplegar. El hará el resto.

Si aun así, no encontramos plantillas para las aplicaciones, podemos crear las nuestras desde el botón **_Add template_**. O buscar por internet un listado más completo de plantillas y añadirlas desde `Settings -> App Templates -> Use external templates`.

![Portainer - Plantillas externas](/assets/posts/es/docker-y-portainer-en-debian/portainer-templates-external.jpg)

Recomiendo la lista [**_SelfHosted templates_**](https://raw.githubusercontent.com/SelfhostedPro/selfhosted_templates/master/Template/template.json) de [_SelfHostedPro_](https://github.com/SelfhostedPro/selfhosted_templates).

## Bonus 2: Mantén actualizados tus contenedores

Con el sistema de contenedores, **los archivos de las aplicaciones suelen ser fijos**, es decir, las carpetas y archivos guardados dentro del volumen suelen ser los archivos que cambian, configuraciones, datos... pero el resto del contenedor tiene archivos que no cambian con su uso. Es por ello que, aunque en algunos casos, si actualizamos una aplicación con el típico dialogo, puede que la aplicación sea actualizada. **Lo apropiado es actualizar el contenedor con la nueva imagen** que contendrá la nueva versión y guardar solo los datos persistentes guardados en el volumen.

Para ello, basta con entrar en el contenedor desde _Portainer_ y hacer clic en **Recreate**. Si el contenedor está configurado con una rama descargará la última imagen de esa rama y recargará el contenedor desde cero, respetando el contenido del volumen.

![Portainer - Recrear contenedor](/assets/posts/es/docker-y-portainer-en-debian/portainer-recreate.jpg)

Pero **¿cómo saber si hay imágenes con actualizaciones pendientes?** Muchas veces no nos interesará actualizar, pero **@SolarNeron** comparte con nosotros una muy buena opción para ser notificados de nuevas versiones. La mayoría de imágenes se hospedan y crean despliegues con nuevas versiones en repositorios de _GitHub_. Podemos aprovechar esta característica en nuestro favor.

Por ejemplo, [este es el repositorio de la imagen de _Portainer_](https://github.com/portainer/portainer).

![Portainer - GitHub Portainer](/assets/posts/es/docker-y-portainer-en-debian/releases-github.jpg)

Desde _GitHub_, solo será necesario hacer clic en `Watch -> Releases only` y **recibiremos una notificación en _GitHub_ y en nuestro correo** cada vez que se despliega una nueva versión. De nuevo, solo tendremos que recrear el contenedor para actualizar la imagen y la aplicación.

## Conclusión

**Usando _Docker_ podremos desplegar y gestionar aplicaciones en nuestro servidor de una manera sencilla pero muy potente**, pudiendo controlar los datos persistentes y ser capaces **con _Portainer_ de llevar toda esta gestión de una manera visual**. Intenta probar algunas aplicaciones que veas interesantes para ir aprendiendo como el sistema de virtualización por contenedores puede ser una de las mejores alternativas para tu servidor.
