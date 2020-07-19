---
title: "Installing Debian 10 from scratch"
header:
  image: /assets/posts/en/installing-debian-10-from-scratch/header.jpg
categories:
  - Tutorial
  - PC   
  - Linux
lang: en
ref: 34
permalink: /en/installing-debian-10-from-scratch/
---

After trying [*Home Assistant*](https://www.home-assistant.io/) on a [*Raspberry Pi 2*](https://www.raspberrypi.org/) for several months, it became necessary to buy a *Mini-PC* in order to expand the services and devices in my home, that is why I have encouraged myself to buy a [***Medion S22003 MD34639***](https://amzn.to/3j8XQvN).

[*Home Assistant Add-ons*](https://www.danielmartingonzalez.com/en/meeting-home-assistant/#installing-our-first-add-on) are quite useful, they allow you to configure new services in a simple way. But many times, they fall short in functionality and not all services are available as *Add-on*. So I decided to install a Linux-based operating system, [*Docker*](https://www.docker.com/) and all services as *Docker* containers. This will ease the task of keeping the settings and data of each "application", and the quick update and management of each one separately without affecting the rest.

In this list, the first point is to install [***Debian 10***](https://www.debian.org/) from scratch on the *Mini-PC*.

## Requirements

- The computer/*Mini-PC*/*NUC*... where we are going to install *Debian 10*.
- A *USB* flash drive.
- A computer to create an installation image on the *USB* drive.

## Download and burn the image

I have chosen to do an installation with a boot *USB* containing the *Debian 10* image. The first thing to do is download the image and burn it to the *USB* stick using [***Rufus***](https://rufus.ie/).

[From the page for obtaining *Debian 10*](https://www.debian.org/distrib/), select the image for your machine's architecture. In my case, *64-bit PC*.

![Debian 10 image download](/assets/posts/en/installing-debian-10-from-scratch/download-image.jpg)

Next, download *Rufus* from its [official website](https://rufus.ie/). You can also use any other bootable *USB* software.

![Rufus settings](/assets/posts/en/installing-debian-10-from-scratch/rufus.png)

Insert the *USB* drive into the computer. Open *Rufus*, load the recently downloaded image into the program, and select the *USB*. The rest of the options can be left as default.

Click on **Start**. When are done, close the window and unplug the *USB*.

## Installing Debian 10

**Insert the *USB* flash drive into the *Mini-PC*** where you are going to install *Debian 10*, and turn it on.

A screen with an installation menu will appear. Click on **Install**.

![Installer menu](/assets/posts/en/installing-debian-10-from-scratch/installer-menu.jpg)

### Language settings

Select the language, location, and keyboard map.

![Language selection](/assets/posts/en/installing-debian-10-from-scratch/language-selection.png)

### Network configuration

The network settings will then be loaded so that the necessary files can be downloaded during installation. In my case, the computer has a *Wi-Fi* connection and a network cable. The first one uses a proprietary driver, so during the installation it becomes difficult to configure it, I choose to connect the network cable and configure the device later.

![Network hardware error](/assets/posts/en/installing-debian-10-from-scratch/network-hardware-error.png)

Insert a hostname and a domain name. The domain is usually the same for all devices in the house.

![Hostname](/assets/posts/en/installing-debian-10-from-scratch/hostname.png)

### User accounts

In the user account configuration, define a password for the user `root` (administrator), and for the user account to be used usually with less privileges: full name, username, and password.

![root user password](/assets/posts/en/installing-debian-10-from-scratch/root-password.png)

![basic user fullname](/assets/posts/en/installing-debian-10-from-scratch/user-fullname.png)

### Disk partition setup

Since the computer will be used exclusively as a server and almost all services will be encapsulated in *Docker* containers, the hard disk will be configured to use a single disk partition, select the internal hard disk (be careful not to select the *USB* memory), and indicate that all files will be saved in a single partition.

![Guided disk partition](/assets/posts/en/installing-debian-10-from-scratch/guided-partition-disk.png)

![Disk selection](/assets/posts/en/installing-debian-10-from-scratch/disk-selection.png)

![One file partition](/assets/posts/en/installing-debian-10-from-scratch/one-partition.png)

At the moment, *RAID*, *LVM* will not be configured... Save.

### Repositories configuration

Select the *Debian* mirror country for the package manager, and a Web address of your choice for your country. If you don't know the nearest mirror Web address, choose `deb.debian.org`.

![One partition for all files](/assets/posts/en/installing-debian-10-from-scratch/repository-selection.png)

If you have a *Proxy* to access your network, it can be configured in the next step, usually you have none, so it can be left blank.

![Proxy settings](/assets/posts/en/installing-debian-10-from-scratch/proxy-settings.png)

### Software selection

Finally, in the software selection window, since at least in my case I don't want a desktop environment. I only select ***SSH* Server** and **System Utilities**.

![Software selection](/assets/posts/en/installing-debian-10-from-scratch/software-selection.png)

### Finish the installation

Wait a few minutes until the installation is complete. Remove the *USB* flash drive from the computer and click continue to restart the computer.

![Installation complete](/assets/posts/en/installing-debian-10-from-scratch/installation-complete.png)

By choosing the installation without desktop, when loading the system a console window will be shown asking for a user and its password, you can use `root` or the user you have created in the user accounts configuration step.

```bash
Debian GNU/Linux 10 n-primaryserver tty1

n-primaryserver login: _
```

## Bonus: Install sudo

`sudo` is a command that used before another command causes that command to be executed with the privileges of another user (usually `root`). It is widely used to be able to use commands that need privileges without having to change users.

By default, `sudo` is not installed in *Debian*. To install it, log in as `root` user or activate super user mode with:

```bash
$ su -
```

Install `sudo` executing:

```bash
$ apt-get install sudo -y
```

Gives permissions to use `sudo` to the current user with:

```bash
$ usermod -aG sudo YOURUSER
```

The next line has yet to be added to the `sudoers` file, modifying the file with `visudo` command:

```bash
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```

Restart the computer to make the changes effective.

## Bonus 2: Update sources.list

`sources.list` is a file that indicates the repositories where *Debian* will look for *firmware*, *software*... It is necessary to update it in order to have resources that by default are not accessible. Execute the command:

```bash
$ sudo nano /etc/apt/sources.list
```

This will open a text editor. The idea is to add `contrib` and `non-free` to access a larger package collection. In my case, `sources.list` looks like this:

```bash
# deb cdrom:[Debian GNU/Linux 10.4.0 _Buster_ - Official amd64 NETINST 20200509-10:25]/ buster main

deb http://deb.debian.org/debian/ buster main contrib non-free
deb-src http://deb.debian.org/debian/ buster main contrib non-free

deb http://security.debian.org/debian-security buster/updates main contrib non-free
deb-src http://security.debian.org/debian-security buster/updates main contrib non-free

# buster-updates, previously known as 'volatile'
deb http://deb.debian.org/debian/ buster-updates main
deb-src http://deb.debian.org/debian/ buster-updates main
```

Then save by pressing **F3** and exit the editor with **F2**.

Finally, execute the commands:

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrade
```

## Conclusion

The computer is configured in a basic way with ***Debian 10***, with the bonus of having available the widely used `sudo` command and access to a larger catalog of packages. The next steps are to install *Docker* and the containers we want as services so that our computer becomes a powerful home server.
