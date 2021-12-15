---
title: "Online file browser for your server"
header:
  image: /assets/posts/en/online-file-browser-for-your-server/header.png
categories:
  - Tutorial
  - PC
  - Linux
lang: en
ref: 40
permalink: /en/online-file-browser-for-your-server/
last_modified_at: 2021-12-15
---

When [installing an operating system without a desktop](https://www.danielmartingonzalez.com/en/installing-debian-from-scratch/), one of the things that is most missed is **having a file explorer** with which to perform basic tasks. [**_File Browser_**](https://filebrowser.org/) fills this need by allowing **access to files on the server through the web browser**.

As if that were not enough, it also includes a **file viewer and editor**, and a **terminal to execute commands**.

![File Browser](/assets/posts/en/online-file-browser-for-your-server/file-browser.png)

## Installation and Use

Its installation, as usual, will be done by [instantiating a _Docker_ container](https://www.danielmartingonzalez.com/en/docker-and-portainer-in-debian/) by executing the following command:

```bash
$ docker run -d --name=Filebrowser --hostname=filebrowser --restart=always -p 8086:80 -v /:/srv -e TZ="Europe/Madrid" filebrowser/filebrowser:latest
```

- `docker run -d`: Create a container with the following configuration if it does not exist and start it in background.
- `--name=Filebrowser`: The name of the container. This is important because you can have several containers based on the same image.
- `--hostname=filebrowser`: The name of the container machine. Optional.
- `--restart=always`: Restart the container if it stops. If it stops manually, it is only restarted when the container is manually restarted, or the _Docker_ service is restarted.
- `-p 8086:80`: _File Browser_ exposes port `80` for File Explorer access. As port `80` is usually used for any _Web_ service; We indicate that port `80` of the container is exposed for example in port `8086`. In this way we make other services that want to use port `80` to use it.
- `-v /:/srv`: Links a folder on the server (left), with a folder on the container (right). _File Browser_ uses by default the internal root `/srv` folder to display in the file explorer. By indicating the root `/` in the server folder, we indicate that we want to show all the files on the server.
- `-e TZ='Europe/Madrid'`: The time zone of the container machine. Optional. It is very useful so that the logs and notifications are with the local time.
- `filebrowser/filebrowser:latest`: Indicates the image used to mount the container. If no version is indicated, it will take the latest stable version.

Next, open a _Web_ browser and enter the address: `http://YOUR_IP_SERVER:8086`. In the next window, the username and password are `admin`.

![File Browser - Login](/assets/posts/en/online-file-browser-for-your-server/file-browser-login.png)

From here, you will see the folders and files on the server, and you will be able to perform the typical actions that you would do in any File Explorer.

## Options

From the **Settings section** you can change the user's language or password, but also other settings such as allowing the creation of other users or activate dark mode.

![File Browser - Settings](/assets/posts/en/online-file-browser-for-your-server/file-browser-settings.png)

## Conclusion

Now you can access the files on the server using a **File Browser**. A comfortable and visual way to work.
