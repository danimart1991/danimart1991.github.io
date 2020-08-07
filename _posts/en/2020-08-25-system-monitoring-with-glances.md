---
title: "System monitoring with Glances"
header:
  image: /assets/posts/en/system-monitoring-with-glances/header.jpg
categories:
  - Tutorial
  - PC
  - Linux
lang: en
ref: 38
permalink: /en/system-monitoring-with-glances/
---

[Once you are familiar with *Docker*](https://www.danielmartingonzalez.com/en/docker-and-portainer-in-debian/) and have created several containers, it is the best time to **monitor the server**. You will monitor the resources and see if any containers are using them more than they should.

[***Glances***](https://nicolargo.github.io/glances/) is a monitoring tool written in Python that will give you the minimal but critical system information in the terminal itself or in a Web browser.

Let's look at two ways to install *Glances*.

## Installation and use

### Installation with Script

You can easily install *Glances* as server software using the following *Script*:

```bash
$ curl -L https://bit.ly/glances | /bin/bash
```

To use it, just run the following command:

```bash
$ glances
```

![Glances Terminal](/assets/posts/en/system-monitoring-with-glances/glances-terminal.png)

This method is the easiest, but *Glances* and its dependencies will remain installed on the system, making it more difficult to uninstall or upgrade. **Recommended installation using a *Docker*** container.

### Docker installation

Although it may seem more complicated, if we already have the basic notions of *Docker*, the creation of the *Glances* container is quite simple, just execute the following command:

```bash
$ docker run -d --name=Glances --hostname=Glances --restart="always" --network=host -e GLANCES_OPT="-w" -v glances_config:/glances/conf -v /var/run/docker.sock:/var/run/docker.sock:ro --pid host docker.io/nicolargo/glances
```

As it is a command with many arguments, I will explain each one separately:

- `docker run -d`: Create a container with the following configuration if it does not exist and start it in background.
- `--name=Glances`: The name of the container. This is important because you can have several containers based on the same image.
- `--hostname=Glances`: The name of the container machine. Optional.
- `--restart=always`: Restart the container if it stops. If it stops manually, it is only restarted when the container is manually restarted, or the *Docker* service is restarted.
- `--network=host`: The `host` networking uses the IP address of the host running docker such that a container's networking appears to be the server rather than separate.
- `-e GLANCES_OPT="-w"`: With this argument *Glances* will start the Web server to be able to see the information from any Web browser.
- `-v glances_config:/glances/conf`: Links the *Glances* settings to a volume so that the data persists even if we delete the container.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Links a server folder (left), with a container folder (right). In this case as *Portainer* will have access to the server containers, it links its information with the `docker.sock` file.
- `--pid host`: Indicates to use the namespace of the *Host* for the processes. This parameter gives the container access to the server processes.
- `docker.io/nicolargo/glances`: Indicates the image used to mount the container. If no version is specified, it will take the latest stable version.

Then, having used the `-e GLANCES_OPT="-w"` option, a Web server will be created to look up the data on port `61208`. Enter the Web address `http://SERVERIP:61208` and you will see all the system and container information.

![Glances Web](/assets/posts/en/system-monitoring-with-glances/glances-web.png)

## Bonus: Integración en Home Assistant

To avoid having to look up *Glances* in the terminal or on the Web page from time to time, we can [integrate *Glances* in *Home Assistant*](https://www.home-assistant.io/integrations/glances/) and, by the way, integrate it into your notification system and receive warnings if any sensor exceeds the established limits.

The only requirement is to have started *Glances* with the Web server enabled, that is with the `-w` parameter.

```bash
$ sudo glances -w
Glances web server started on http://0.0.0.0:61208/
```

> This parameter was already included when creating the *Docker* container in the previous section.

In *Home Assistant*, include the integration from `Configuration -> Integrations -> Add Integration (Button +)`. Look for *Glances* and configure the fields in the following window. If you have followed the article, and *Home Assistant* is installed on the same machine as *Glances*, there is no need to change anything.

![Glances Integration](/assets/posts/en/system-monitoring-with-glances/home-assistant-glances.png)

**An integration with several entities** will be created that you can include in the *Dashboard* or use in automations.

## Conclusión

*Glances* may not be the most powerful system monitoring software, but it provides the most critical information to detect problems at a glance. Its installation is just a command and consumes almost no resources.
