---
title: "Webmin: Managing a server from the browser"
header:
  image: /assets/posts/en/webmin-managing-server-from-browser/header.png
  teaser: /assets/posts/en/webmin-managing-server-from-browser/teaser.png
categories:
  - Tutorial
  - Server
tags:
  - Webmin
  - Server
  - Tutorial
  - Management
lang: en
ref: 47
permalink: /en/webmin-managing-server-from-browser/
---

I don't know much about _Unix_ systems. While I can type some commands and move around its directories, it's not something I'm used to or like to deal with. But I recognize that a _Linux_ distribution is the best option to set up a server.

[![Webmin's System information start screen (Dashboard)](/assets/posts/en/webmin-managing-server-from-browser/webmin-dashboard.png)](/assets/posts/en/webmin-managing-server-from-browser/webmin-dashboard.png)

[**_Webmin_**](https://webmin.com/) solves the management of a server in an elegant way, since it is a system configuration tool accessible via the web for _Unix_ systems. With it, you can configure user accounts, view and edit files, view the status of system resources, and much more from the comfort of your browser. This application helps us eliminate the need to edit the configuration and service files manually and provides an interface to do it easily and with little tips.

> **WARNING**: _Webmin_ allows you to modify settings and data at the core level of the system, this means that before touching any module, it is advisable to read documentation about how it works and how it can affect our server.

## Installation and Use

We are going to install _Webmin_ on the system itself by `apt` package. The reason for doing it this way and not in a _Docker_ container is because _Webmin_ needs to have full access to the entire system, both files and services. Also, if for any reason the _Docker_ service goes down, you can still use _Webmin_ to fix the problem.

Assuming that your server has a distribution based on _Debian_, _Ubuntu_ or _Mint_. Execute the following commands:

```bash
$ sudo wget -qO - http://www.webmin.com/jcameron-key.asc | sudo apt-key add -
$ sudo sh -c 'echo "deb http://download.webmin.com/download/repository sarge contrib" > /etc/apt/sources.list.d/webmin.list'
$ sudo apt install apt-transport-https -y
$ sudo apt update
$ sudo apt install webmin -y
```

> For the rest of the distributions and available installation types, you can consult [this _Wiki_](http://doxfer.webmin.com/Webmin/Installation).

And that's all, finally, open a browser and access the address `https://<ip_of_your_server>:10000`. On the main screen (_Dashboard_) you will have summary information on the status of your server. In the bar on the left you will have different utilities to manage the server. Remember, before touching, it is convenient to read the documentation of that module and tool.

> It is possible that, when entering for the first time, a certificate error is displayed, agree to enter in a non-secure way.

## References

- [Webmin - Official Web](https://webmin.com)
- [Webmin - Wiki](https://doxfer.webmin.com/Webmin)
- [Raiola Networks - Manual completo Webmin, Usermin y Virtualmin](https://raiolanetworks.es/blog/webmin-usermin-virtualmin)
- [Linuxize - How to Install Webmin on Debian 10 Linux](https://linuxize.com/post/how-to-install-webmin-on-debian-10/)
