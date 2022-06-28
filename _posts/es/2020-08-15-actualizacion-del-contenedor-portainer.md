---
title: "Actualización del contenedor Portainer"
header:
  image: /assets/posts/es/actualizacion-del-contenedor-portainer/header.png
toc: false
classes: wide
categories:
  - Tutorial
  - PC
  - Linux
lang: es
ref: 37
permalink: /es/actualizacion-del-contenedor-portainer/
last_modified_at: 2022-06-28
---

[En el artículo anterior se vio como **instalar *Docker* y *Portainer***](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian). Una opción fantástica para gestionar contenedores *Docker* de manera visual.

[Una de las partes enseñaba como **actualizar estos contenedores desde *Portainer***](https://www.danielmartingonzalez.com/es/docker-y-portainer-en-debian/#bonus-2-mant%C3%A9n-actualizados-tus-contenedores). Dar al botón ***Recreate***, y *Portainer* elimina y crea de nuevo el contenedor, de paso, descarga la nueva imagen actualizada si así se desea.

Salvo cambios de configuración de una versión a otra, esto suele funcionar en todos los contenedores, a excepción del propio *Portainer*. Es por eso que hay que **realizar una actualización manual**.

Los pasos a seguir son sencillos:

- Parar el contenedor.
- Eliminar el contenedor
- Descargar la nueva imagen.
- Crear y ejecutar de nuevo el contenedor.

Abre una terminal y escribe los siguientes comandos:

```bash
$ docker stop portainer
$ docker rm portainer
$ docker pull portainer/portainer-ce
$ docker run -d --name=portainer --hostname=portainer --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data -e TZ='Europe/Madrid' portainer/portainer-ce
```

> A partir de la versión **2.0.0** de **Portainer**, la imagen pasa a ser llamada `portainer-ce`.

Una vez finalizado, accede a la dirección `http://IPDEPORTAINER:9000` para comprobar que *Portainer* está actualizado.
