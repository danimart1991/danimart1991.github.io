---
title: "Automatically update containers"
header:
  image: /assets/posts/en/automatically-update-containers/header.png
categories:
  - Tutorial
  - Docker
  - Linux
lang: en
ref: 39
permalink: /en/automatically-update-containers/
---

As we expand our collection of containers [*Docker*](https://www.docker.com/), it becomes tedious to keep an eye on possible updates of the applications we use and even to completely forget about updating those that are usually used in the background.

**[*Ouroboros*](https://github.com/pyouroboros/ouroboros) is a container that updates other containers** to the latest version on a recurring basis so that we don't have to do it ourselves.

## *Ouroboros*

*Ouroboros* monitors running containers (all or only those specified) and every few minutes checks if there is an updated image for any of them and updates it to the latest version. Also, it keeps all the configuration with which the container was raised, so we won't lose anything.

> It is possible that the new image will result in a change of the container configuration. In these cases, the update will fail. It is recommended to use *Ouroboros* on stable images with a path.

## Installation

Although ***Ouroboros* saves its settings in a [*YAML*](https://yaml.org/)** file that can be edited to configure different aspects of the application. One of the advantages it has is that you can configure almost everything by environment variables when build up the container instance. So, it is not necessary to mount a volume linked to the host to persist the data.

To **load an instance**, just execute the following command:

```bash
$ docker run -d --name=Ouroboros --hostname=ouroboros --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -e CLEANUP=true -e TZ='Europe/Madrid' -e INTERVAL=300 pyouroboros/ouroboros:latest
```

I will explain each section:

- `docker run -d`: Create a container with the following configuration if it does not exist and start it in background.
- `--name=Ouroboros`: The name of the container. This is important because you can have several containers based on the same image.
- `--hostname=ouroboros`: The name of the container machine. Optional.
- `--restart=always`: Restart the container if it stops. If it stops manually, it is only restarted when the container is manually restarted, or the *Docker* service is restarted.
- `--network=host`: The `host` networking uses the IP address of the host running docker such that a container's networking appears to be the server rather than separate.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Links a server folder (left), with a container folder (right). In this case as *Ouroboros* will have access to the server containers, it links its information with the `docker.sock` file.
- `-e CLEANUP=true`: Deletes old images after updating.
- `-e TZ='Europe/Madrid'`: The time zone of the container machine. Optional. It is very useful for logs and notifications to be in local time.
- `-e INTERVAL=300`: Interval to check for updates. Default: 5 minutes.
- `pyouroboros/ouroboros:latest`: Indicates the image used to mount the container. If no version is specified, it will take the latest stable version.

**Once executed, the containers will begin to update. In the case that we do not want a container to be updated, you can use the environment variable `-e IGNORE="Container1 Container2 Conta..."`.

## Notifications

In addition, *Ouroboros* includes [*Apprise*](https://github.com/caronc/apprise) in its code, which is a **notification service** that includes most commonly used services and applications.

This allows you to receive notifications in the application or service of your choice every time a container is updated. And the best part is that you only need to include a couple more parameters in the *Docker* command.

For example, you can create a [*Bot* from *Telegram*](https://www.danielmartingonzalez.com/en/home-assistant-notifications-on-telegram/#bots-on-telegram), get its *API Token* and the *Chat ID* and add them to the command with the environment variable `NOTIFIERS="tgram://BOT_API_TOKEN/YOUR_CHAT_ID/"`.

```bash
$ docker stop Ouroboros
$ docker rm Ouroboros
$ docker run -d --name=Ouroboros --hostname=ouroboros --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -e CLEANUP=true -e TZ='Europe/Madrid' -e INTERVAL=300 -e NOTIFIERS="tgram://1234567890:1234567890abcdefghijklmnopqrstuvwxyz/0987654321/" pyouroboros/ouroboros:latest
```

**As soon as an update is made, you will be notified.**

![Telegram Notifications](/assets/posts/en/automatically-update-containers/telegram-notification.png)

You can include [all the services of *Apprise*](https://github.com/caronc/apprise#supported-notifications) that you want simply by leaving a space between one and another in the variable `NOTIFIERS`.

## Conclusion

All your containers will now be updated to the latest version without the need to keep an eye on new images and also receive a notice to check what's new or if there is any change in the configuration that forces you to make a small change. All this with a simple container that does not need backup or maintenance.
