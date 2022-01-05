---
title: "Your files from Windows, installing a Samba Server on Debian "
header:
  image: /assets/posts/en/samba-server-in-debian/header.png
  teaser: /assets/posts/en/samba-server-in-debian/teaser.png
categories:
  - Tutorial
  - Debian
tags:
  - Server
  - Debian
  - Tutorial
  - Samba
  - SMB
  - Windows
lang: en
ref: 46
permalink: /en/samba-server-in-debian/
---

Despite having different ways of [accessing files on your server](https://www.danielmartingonzalez.com/en/online-file-browser-for-your-server/), perhaps all of them become somewhat tedious if we are used to browsing through _Windows_ folders. I have recently discovered a very simple way to have [**a _Samba_ file server**](https://www.samba.org/) in [_Debian_](https://www.debian.org/index.es.html) and derivatives to be able to access the files on the server from a _Windows_ client as if it were another folder.

Why do we need a _Samba_ server to access files from _Windows_ on a _Linux_ server? Okay. By default, both systems use different file sharing protocols. _Samba_ is a free implementation of the _Microsoft Windows File Sharing Protocol_ (formerly called _SMB_, recently renamed _CIFS_) for _UNIX-like_ systems.

## Installation

> Most of the following commands require `root` privileges to run. You can use `sudo` or log in as `root` user and save yourself typing the prefix in each command.

Install the _Samba server_ package:

```shell
$ sudo apt update
$ sudo apt install samba -y
```

Enable the _Samba_ service to start with the system:

```shell
$ sudo systemctl enable smbd
```

If you use the system _firewall_, you need to open the following ports:

```shell
$ sudo ufw allow 139
$ sudo ufw allow 445
```

Restart the `smbd` service and check that it's running:

```shell
$ sudo systemctl restart smbd
$ sudo systemctl status smbd

  ? smbd.service - Samba SMB Daemon
  ...
  Status: "smbd: ready to serve connections..."
  ...
```

## Setting up Samba Share

Edit the `smb.conf` file with the command `sudo nano /etc/samba/smb.conf`, adding the following to the end of the document:

```config
[media]
comment = media folder
path = /media
writeable = yes
browsable = yes
public = no
valid users = danimart1991, root
```

I attach an explanation of each line:

- `[media]`: It is the short name that describes what we are going to share. An identifier.
- `comment`: A comment to describe what we are sharing.
- `path`: The path of the folder we want to share.
- `writeable`: Indicates if we want the folder to be read-only or if you want to allow writes and deletions to be carried out on it.
- `browsable`: If this share is seen in the list of available shares in a network view and in the navigation list.
- `public`: Indicates if the folder does NOT need a password to access it.
- `valid users`: List of users who will have access to the folder.

> You can see more options in the [documentation of the configuration file](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html).

We save the file with the `F3` key and exit with `F2`.

## Setting up Users

As you can see, I have indicated in the `valid users` field which users can access the files. These users must have access to the shared folder and its files, otherwise they will not be able to see them when access it from _Windows_.

In addition, these users have to be created on our server. If not, just use the command:

```shell
$ sudo useradd danimart1991
```

Next. You have to declare that user in _Samba_ and assign a unique password for this service. For this, use the following command:

```shell
$ sudo smbpasswd -a danimart1991
New SMB password: xxxxxxxx
Retype new SMB password: xxxxxxxx
```

Finally, restart the service:

```shell
$ sudo systemctl restart smbd
```

## Conclusion

We have already configured our _Samba_ server for access from _Windows_. It is enough to open a _File Explorer_, write the server address and access indicating the credentials that have been previously created.

[![Server Access from Windows with Samba](/assets/posts/en/samba-server-in-debian/windows-samba.png)](/assets/posts/en/samba-server-in-debian/windows-samba.png)

Now you will have quick and easy access from your _Windows_ devices to the server folders.

## References

- Thanks to José Antonio Simancas (_JASimancas_)
- [Samba](https://www.samba.org/)
- [Server World - Debian 11 Bullseye : Samba](https://www.server-world.info/en/note?os=Debian_11&p=samba&f=1)
- [Linuxito - Instalar y configurar un servidor Samba en Debian](https://www.linuxito.com/gnu-linux/nivel-basico/1233-instalar-y-configurar-un-servidor-samba-en-debian)
- [Unixcop - How to configure Samba Server with Debian 11](https://unixcop.com/how-to-configure-samba-server-with-debian-11/)
- [smb.conf — The configuration file for the Samba suite](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html)
