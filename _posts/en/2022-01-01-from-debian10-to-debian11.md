---
title: "From Debian 10 to Debian 11"
header:
  image: /assets/posts/en/from-debian10-to-debian11/header.png
  teaser: /assets/posts/en/from-debian10-to-debian11/teaser.png
categories:
  - Tutorial
  - Debian
tags:
  - Server
  - Debian
  - Tutorial
  - Updates
lang: en
ref: 45
permalink: /en/from-debian10-to-debian11/
---

[_Debian 11_ (_bullseye_)](https://www.debian.org/News/2021/20210814) has been available for use on the stable channel for several months now. This release contains a total of 59,551 packages of which more than 11,294 are new, and this despite a significant reduction of more than 9,519 packages that were marked as obsolete and were removed. 42,821 packages have been updated and 5,434 remain unchanged. That is, a large number of improvements and bug fixes that will improve our server.

**Today we are going to see how to upgrade our system from _Debian 10_ to _Debian 11_ in a few steps.**

## Current system backup

The first and foremost thing is **to have a backup of the system**. The update should not break anything, but just in case. [We are going to make a backup with _Duplicati_](https://www.danielmartingonzalez.com/en/backups-towards-docker/).

## Updating current packages

**Update existing _Debian 10_ packages to the latest version** using the following commands:

```shell
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
$ sudo apt-get dist-upgrade -y
$ sudo apt-get autoremove -y
$ sudo apt-get clean -y
$ sudo reboot
```

## Modifying the repositories

**Modify the list of system repositories** with the command `sudo nano /etc/apt/sources.list` replacing those that contain the word `buster` with the following:

```
deb http://deb.debian.org/debian/ bullseye main contrib non-free
deb-src http://deb.debian.org/debian/ bullseye main contrib non-free

deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb-src http://security.debian.org/debian-security bullseye-security main contrib non-free

deb http://deb.debian.org/debian/ bullseye-updates main
deb-src http://deb.debian.org/debian/ bullseye-updates main
```

## (Optional) Update Docker key

If we have _Docker_ installed, it is recommended to update the _GPG key_ with the following commands:

```shell
$ sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ sudo echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

If it asks to overwrite, confirm.

## Upgrading to Debian 11

At last, **run the following commands to upgrade your system to _Debian 11_**.

```shell
$ sudo apt-get update -y
$ sudo apt-get full-upgrade -y
$ sudo reboot
```

> During installation, keep the local settings in case you ask us to make changes (this is usually the default option).

## Updating and cleaning packages

Finally, **update all installed packages** and perform a cleanup of obsolete packages with the following commands:

```shell
$ apt-get update -y
$ apt-get upgrade -y
$ apt-get dist-upgrade -y
$ apt-get autoremove -y
$ apt-get clean -y
$ sudo reboot
```

Repeat this list of commands again until the result of each one indicates that no more packages have to be installed, updated and/or deleted.

## Conclusion

You can check the version of _Debian_ installed on the system with the command `lsb_release -a`.

```
Distributor ID: Debian
Description:    Debian GNU/Linux 11 (bullseye)
Release:        11
Codename:       bullseye
```

Following the list of commands in this tutorial, our server will be updated to _Debian 11_ and we will have stable support for 5 years. Remember to keep the packages updated to avoid different failures and to have a healthy system.

## References

- Thanks to José Antonio Simancas (_JASimancas_)
- [Cybercity - How to upgrade Debian 10 to Debian 11 Bullseye using the CLI](https://www.cyberciti.biz/faq/update-upgrade-debian-10-to-debian-11-bullseye/)
- [Docker Docs - Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/)
