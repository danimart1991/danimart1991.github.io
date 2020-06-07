---
title: "No más publicidad en tu red con Pi-hole"
header:
  image: /assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/header.jpg
categories:
  - IoT
  - Home Assistant
lang: es
ref: 33
permalink: /es/no-mas-publicidad-en-tu-red-con-pihole/
---

Somos muchos los que solemos tener algún tipo de bloqueador de publicidad en los navegadores *Web* (*Adblock*, *uBlock*...). Uno de los mayores problemas que plantea esta solución es la difícil instalación de estos mismos bloqueadores en algunos dispositivos como son las *Smart TVs*, o que por ejemplo en móvil, solo se realiza el bloqueo a nivel de navegador, pero no a nivel del resto de aplicaciones.

Además, hay otros componentes molestos como el *Malware* y la telemetría que muchos programas o páginas *Web* usan con demasiada frecuencia y son difíciles de evitar.

Una forma bastante más eficaz de evitar todo este contenido desagradable es usando bloqueadores que actúen a nivel de red y no solo en cada dispositivo. De esta manera nos ahorramos configurar todos los dispositivos de la casa y nos aseguramos qué nuestra red está limpia. Gracias a [***JASimancas***](https://github.com/jasimancas) (José Antonio Simancas), he podido conocer y probar [***Pi-hole***](https://pi-hole.net/), uno de los más usados.

Usando una [*Raspberry Pi*](https://www.raspberrypi.org/), vamos a configurar desde cero: *Raspberry Pi OS* + *Pi-hole* + Listas recomendadas.

## Requisitos

- Una *Raspberry Pi*
- Una tarjeta *SD*
- Un ordenador para crear una imagen en la tarjeta *SD*.
- **(Recomendación)** Conectar a la *Raspberry Pi* un cable de red.

## Instalando Raspberry Pi OS

Lo primero es instalar ***Raspberry Pi OS*** (antiguo *Raspbian*) en la *Raspberry Pi*. Si disponemos de otro sistema operativo basado en *Linux* u otro dispositivo ya configurado, se puede pasar a la siguiente sección directamente.

Pasos:

1. [**Descargar *Raspberry Pi Imager***](https://www.raspberrypi.org/downloads/) compatible con el sistema operativo del ordenador.

    > Aunque en el artículo se va a usar *Raspberry Pi Imager* para *Windows*. Todas las versiones son parecidas.

2. **Introducir la tarjeta *SD*** en nuestro ordenador.

3. Instalar y abrir *Raspberry Pi Imager* y seleccionar el **Sistema Operativo** al gusto:

    | | |
    | --- | --- |
    | *Raspberry Pi OS (32-bit)* | Con entorno gráfico |
    | *Raspberry Pi OS Lite (32-bit)* | Sin entorno  (El que usaremos en este artículo) |
    | *Raspberry Pi OS Full (32-bit)* | Con entorno gráfico y aplicaciones recomendadas |

4. Seleccionar la tarjeta *SD* y haz clic en ***Write***.

    ![Raspberry Pi Imager](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/raspberry-pi-imager.jpg)

5. Una vez terminado, **abrimos con el explorador de archivos** la tarjeta recién grabada y dentro creamos el archivo `ssh` sin extensión ni contenido.

    ![Tarjeta SD archivo SSH](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/sd-card-ssh.jpg)

6. **Introducimos la tarjeta *SD* en la *Raspberry Pi*** y la enchufamos a la corriente.

7. Dejamos pasar unos minutos y desde nuestro ordenador intentamos conectarnos por ***SSH*** usando la *IP* asignada automáticamente por el *Router* o con `raspberrypi.local` y el usuario `pi`. La contraseña por defecto es `raspberry`.

    ```bash
    C:\Users\danimart1991> ssh pi@192.168.1.204
    password: raspberry
    ```

8. Una vez dentro, **ejecutamos el comando** `sudo raspi-config`.

    1. Seleccionamos ***Update*** para actualizar los repositorios y el sistema.
    2. Seleccionamos ***Change User Password*** para cambiar la contraseña del usuario pi a una más segura.

9. Ejecutamos `sudo reboot` para **reiniciar la *Raspberry*** y aplicar los cambios.

## Instalando Pi-hole

A continuación, para **instalar *Pi-hole***, simplemente ejecutamos el siguiente comando y seguimos los pasos.

```bash
curl -sSL https://install.pi-hole.net | bash
```

El primer paso es **seleccionar el proveedor de *DNS***, es decir, *Pi-hole* capturará las peticiones que se hagan en nuestra red y usará este proveedor para resolver las direcciones en la *IP* correspondiente filtrando aquellas maliciosas. Cualquier opción es buena.

![Proveedor DNS](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/proveedor-dns.jpg)

Aceptamos las listas por defecto (luego las cambiaremos).

![Listas Comunes](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/listas-comunes.jpg)

Aceptamos los bloqueos por *IPv4* e *IPv6*.

Y en la siguiente pantalla, **¡MUY IMPORTANTE!**, escogemos la *IP* estática (fija) que queremos usar en la *Raspberry Pi*. Esta *IP* se usará luego como proveedor *DNS* en el resto de dispositivos.

Podemos usar la que viene por defecto o cambiarla por otra. Para el artículo se ha seleccionado `192.168.1.3`.

![Configuración IP estática](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/ip-estatica.jpg)

Indicamos en las pantallas posteriores:

- Deseamos usar la interfaz *Web* para administradores. **(Opción 0)**
- Activar el servidor *Web*. **(Opción 0)**
- Guardar un registro de consultas. **(Opción 0)**
- Mostrar todo en el modo privacidad *FTL*. **(Opción 0)**

Esperamos un poco...

![Instalación Completa](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/instalacion-completa.jpg)

La última ventana nos mostrará la información del servidor *DNS* creado, así como los datos para acceder al cliente *Web* y su contraseña. Lo apuntamos todo.

## Pi-hole Admin Console

A partir de ahora, el resto de la configuración de *Pi-hole* puede hacerse desde el panel *Web* disponible en la dirección *Web* que nos han indicado en la ventana anterior (`http://192.168.1.3/admin`). Accedemos desde un navegador e introducimos la contraseña haciendo clic en ***Login***.

![Inicio Sesión Pi-hole](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/iniciar-sesion.jpg)

Una vez dentro accederemos al panel de visualización de datos.

![Dashboard](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/panel-de-datos.jpg)

## Configurando la red

Tendremos que hacer que todos los dispositivos de la red tengan configurado como dirección del servidor *DNS* la *IP* de la propia *Raspberry Pi* que hemos usado de servidor *DNS*.

Lo más sencillo es hacer que el propio *Router* sea el que configure esta *IP*. Para ello, leyendo el manual de nuestro Router, accedemos a la sección de configuración *DNS* y cambiamos las del proveedor de *Internet* por la *IP* de nuestro servidor *DNS* (*Raspberry Pi*).

![Servidor DNS Router](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/servidor-dns-router.jpg)

Al reiniciar los dispositivos con *IP* automática, estos obtendrán automáticamente la *IP* del nuevo servidor *DNS*. Igual para aquellos que tengan asignada *IP* reservada en el *Router*.

Para aquellos con *IP* estática (fija) configurada de manera manual en el propio dispositivo, habrá que cambiar el *DNS* primario en la configuración.

![Configuración DNS Manual](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/configuracion-dns-manual.jpg)

Desde la sección `Network` del panel *Web* de *Pi-hole* podemos ver que dispositivos están conectados a la red y cuales están haciendo uso de *Pi-hole* como filtro.

![Dispositivos usando Pi-hole](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/dispositivos-usando-pihole.jpg)

## Actualizando Listas

Desde la sección `Group Management -> Adlists` se pueden modificar las listas que *Pi-hole* usa para admitir o bloquear direcciones.

![Configuración Listas](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/listas.jpg)

Aunque por *Internet* hay multitud de *Webs* dedicadas a recopilar listas, mi recomendación personal es usar las listas que aparecen en verde en [***Firebog***](https://firebog.net/) y cada pocos meses, volver para revisar si hay nuevas listas disponibles. Las propias listas se autoactualizan, así que no es necesario volver a configurarlas, aunque dentro de ellas añadan o quiten cosas.

Una vez cambiadas, es **MUY** recomendable hacer clic en `Tools -> Update Gravity` para que haga una actualización y compresión de listas.

## Realizando copias de seguridad

Una de las cosas más importantes una vez está todo configurado es **realizar copias de seguridad**, de este modo, ante cualquier fallo crítico podremos realizar una restauración y dejar todo funcionando de nuevo.

*Pi-hole* facilita bastante esta tarea desde su *Panel Web*, sección `Settings -> Teleporter`.

![Teleporter](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/teleporter.jpg)

Desde aquí, tan sencillo como dos opciones, exportar configuración e importar configuración (o alguna parte en concreto).

Es recomendable realizar una copia de seguridad con cada cambio importante o antes de cada actualización de *Pi-hole* o del sistema operativo principal (*Raspberry Pi OS*).

## Conclusión

![Conclusión](/assets/posts/es/no-mas-publicidad-en-tu-red-con-pihole/conclusion.jpg)

A partir de ahora **nuestra red estará libre de dominios maliciosos**, sobre todo publicidad. Todo de una manera sencilla y sin necesidad de configurar cada dispositivo por separado. Hemos instalado *Raspberry Pi OS* y *Pi-hole* y aún queda mucho por explorar de estas herramientas, pero eso lo veremos más adelante.
