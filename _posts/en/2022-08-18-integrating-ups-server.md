---
title: "Integrating a UPS in our server"
header:
  image: /assets/posts/en/integrating-ups-server/header.png
  teaser: /assets/posts/en/integrating-ups-server/teaser.png
categories:
  - Tutorial
  - Homelab
  - Server
tags:
  - Homelab
  - Server
  - Tutorial
  - Sai
  - Ups
  - Usb
lang: en
ref: 48
permalink: /en/integrating-ups-server/
---

Our server is still an electronic device, and although [we have protected your information by software through backup copies](https://www.danielmartingonzalez.com/en/backups-towards-docker/), it is appropriate to also protect the hardware. We do not want the data to be corrupted or any electronic component damaged and we have to replace it due to a power surge or a simple power outage.

**UPS (Uninterruptible Power Supplies)** arise from this need. In a very brief way, they are batteries that, placed between our electronic devices and the current, not only protect the devices from possible problems in the electrical supply, but also, in the event of a power outage, offer energy for a reasonable time so that we can safeguard the data, shut down the device correctly and avoid problems.

Although, the objective of this article is not to describe what a UPS is, what types exist, what you have to look for when you buy one... but the integration of it in our server to be able to monitor the information it offers and to be able to carry out different functions when we are interested in an automated way. I do recommend analyzing that the UPS we buy can be integrated via USB, since not all of them are.

Although compatible with many brands and models (which we will see later), for this tutorial we are going to integrate the [**Salicru SPS 900 ONE**](https://amzn.to/3JYuON6) model using [**_NUT_ (_Network UPS Tools_)**](https://networkupstools.org/).

## NUT Server

[_NUT_ (_Network UPS Tools_)](https://networkupstools.org/) consists of two well-differentiated parts, a **software in server mode**, which obtains the data from the UPS, and a **software in client mode**, which displays the data obtained by requesting that information from the server.

We need to know which USB device our server has assigned to the UPS. To do this, in the terminal of the server that is going to manage the UPS, execute `lusb`, connect the USB cable from the UPS to the server and launch the command again. We will see a new entry:

```bash
...
Bus 002 Device 001: ID 0665:5161 Cypress Semiconductor USB to Serial
...
```

Take note of _Bus_ (`002`) and _Device_ (`001`). With this information we will get the path of the device. Following the example, it would be: `/dev/bus/usb/002/001`.

Next, and as usual in the blog, [we are going to instantiate a _Docker_ container](https://www.danielmartingonzalez.com/en/docker-and-portainer-in-debian/) in order to create a service with the purpose we are looking for. In this case, create a _Network UPS Tools_ server.

Execute the following command in a terminal:

```bash
docker run -d --name=nut-upsd --hostname=nut-upsd --restart=always --network=host --device /dev/bus/usb/002/001 -e UPS_NAME="server_ups" -e UPS_DESC="Server - Salicru SPS 900 ONE" -e UPS_DRIVER="blazer_usb" -e UPS_PORT="auto" -e API_USER="upsmon" -e API_PASSWORD="123456789ABCDEFGH" -e ADMIN_PASSWORD="123456789ABCDEFGH" -e SHUTDOWN_CMD="echo 'Home has no current. Proceeding to shut down...'" upshift/nut-upsd
```

We are going to explain each of the parameters that have been used:

- `docker run -d`: Create a container with the following configuration if it doesn't exist and start it in the background.
- `--name=nut-upsd`: The name of the container. It is important because we can have several containers instantiated from the same image.
- `--hostname=nut-upsd`: Is the name of the host to use inside the container.
- `--restart=always`: Restart the container automatically if it stops. If it is stopped manually, it is only started when the container is manually started or the _Docker_ service is restarted.
- `--network=host`: The host network uses the _IP_ address of the server, so a container's network appears to be the server itself rather than separate.
- `--device /dev/bus/usb/002/001`: We give the container access to the _USB_ device that we discovered earlier.
- `-e UPS_NAME="server_ups -e UPS_DESC="Server - Salicru SPS 900`: It is the name and description that we want to give to the UPS, in case we have more than one.
- `-e UPS_DRIVER="blazer_usb"`: The _driver_ that we are going to use with the UPS. Each brand usually has a different _driver_. Read more below.
- `-e UPS_PORT="auto"`: Indicates the UPS port if by chance you need a specific port. It is not usually necessary to modify this parameter.
- `-e API_USER="upsmon" -e API_PASSWORD="123456789ABCDEFGH"`: Indicates the username and password that we want to use for data read-only mode. It is the user that we will use normally.
- `-e ADMIN_PASSWORD="123456789ABCDEFGH"`: Indicates the password used in administrator mode to perform write functions on the server and the UPS.
- `-e SHUTDOWN_CMD="echo 'Home has no current. Proceeding to shut down...'"`: _script_/command to run when battery is VERY low. For example, perform a server shutdown.
- `upshift/nut-upsd`: Indicates the image used to mount the container. If no version is indicated, it will take the latest stable version.

To know which `UPS_DRIVER` to use, you can consult [the _NUT_ compatibility list](https://networkupstools.org/stable-hcl.html), in case you can't find it, there is a [list of devices tested by the community](https://networkupstools.org/ddl/).

> You also have an example for _Docker Compose_ [here](https://github.com/danimart1991/docker-compose-files/tree/master/nut-upsd).

## Clients

Once the server is instantiated, we can use any client to query data or perform different functions.

The server container itself includes a client. If we have enough knowledge of _Docker_, we can do a *Docker Attach *and execute `upsd <UPS_NAME>` (`UPS_NAME` is the name of the UPS that we have indicated when instantiating the server). Commands can also be run with [upscmd](https://networkupstools.org/docs/man/upscmd.html) and [upsrw](https://networkupstools.org/docs/man/upsrw.html).

### docker-webnut

[![docker-webnut client preview](/assets/posts/en/integrating-ups-server/docker-webnut.png)](/assets/posts/en/integrating-ups-server/docker-webnut.png)

[**_docker-webnut_**](https://github.com/edgd1er/docker-webnut) is another simple client for querying data. We can instantiate a _Docker_ container with the following command:

```bash
docker run -d --name=webnut --hostname=webnut --restart=always --network=host -e UPS_HOST="<IP_SERVIDOR>" -e UPS_PORT="3493" -e UPS_USER="upsmon" -e UPS_PASSWORD="123456789ABCDEFGH" edgd1er/webnut:latest
```

We only need to indicate the _IP_ of the machine to which the UPS is connected via _USB_ (in our example, the _IP_ of the server), the username and password that we indicated when instantiating the _NUT_ server, and the port, which is `3493` by default.

Next, we access a Web browser and indicate the address `http://<Server_IP>:3494`.

### nut-webui

[![nut-webui client preview](/assets/posts/en/integrating-ups-server/nut-webui-dashboard-01.png)](/assets/posts/en/integrating-ups-server/nut-webui-dashboard-01.png)

[![nut-webui client preview detail](/assets/posts/en/integrating-ups-server/nut-webui-dashboard-02.png)](/assets/posts/en/integrating-ups-server/nut-webui-dashboard-02.png)

The [**_nut-webui_**](https://github.com/gpdm/nut) client is somewhat more complex. We need to previously create the files:

- `upsset.conf`
  - Uncomment the line to have `http://<Server_IP>:6544/cgi-bin/nut/upsset.cgi`
  - User _admin_, password, the one generated automatically in the _Docker Log_ or indicated in the creation of the _NUT_ server.
- `hosts.conf`
  - Enter the UPS data.
- `upsstats.html`
- `upsstats-single.html`

> Configuration and sample files: https://github.com/gpdm/nut/tree/master/nut-webui/files/etc/nut

And include them in a folder that we will use when instantiating the container with the following command:

```bash
docker run -d --name=nut-webui --hostname=nut-webui --restart=always -p 6495:80 -v <PATH_FOLDER_CONFIGURATION_FILES>:/etc/nut gpdm/nut-webui:latest
```

Next, we access a _Web_ browser and indicate the address `http://<Server_IP>:3495`.

## Bonus: HomeAssistant integration

We can use [**_Home Assistant_**](https://www.home-assistant.io/) as a client, monitor the values offered by the server and perform different automations depending on the status of the sensors created with these values.

Surely, the automatic detection system of _Home Assistant_ detects _NUT_ in our network, but if it is not the case, it is enough to add the integration from `Settings -> Devices & Services -> Add Integration -> Network UPS Tools (NUT)`.

[![NUT Configuration in Home Assistant](/assets/posts/en/integrating-ups-server/nut-homeassistant-config.png)](/assets/posts/en/integrating-ups-server/nut-homeassistant-config.png)

We indicate the data of our _NUT_ server as we have done in other clients and that's it.

[![Home Assistant NUT UPS Sensors](/assets/posts/en/integrating-ups-server/nut-homeassistant-sensors.png)](/assets/posts/en/integrating-ups-server/nut-homeassistant-sensors.png)

## Bonus 2: CheckMK integration

Although we have not talked about [**_CheckMK_**](https://checkmk.com/) in this blog, it is a monitoring and alert system that I use frequently. If you are familiar with it, you may be interested in the following _Plugin_ to monitor _UPS_ data: https://github.com/danimart1991/checkmk_nut

[![CheckMK NUT UPS Sensors](/assets/posts/en/integrating-ups-server/nut-checkmk-sensors.png)](/assets/posts/en/integrating-ups-server/nut-checkmk-sensors.png)

## References

- [Random Bits - Monitoring a UPS with nut on Debian or Ubuntu Linux](https://blog.shadypixel.com/monitoring-a-ups-with-nut-on-debian-or-ubuntu-linux/)
- [Domology - Integrar un SAI en Home Assistant](https://domology.es/integrar-un-sai-en-home-assistant/)
- [Network UPS Tools - Documentation](https://networkupstools.org/documentation.html)
- [Tinkerings - UPS monitoring with NUT, Nagios and Check_MK](https://blog.minodudd.com/2013/10/23/ups-monitoring-with-nut-nagios-and-check_mk/)
- [Professional Review - SAI: Qué es, para qué sirve y que tipos hay en el mercado](https://www.profesionalreview.com/2019/02/23/que-es-sai/)
