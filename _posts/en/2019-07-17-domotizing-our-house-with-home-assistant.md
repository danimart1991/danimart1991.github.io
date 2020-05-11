---
title: "Domotizing our house with Home Assistant"
header:
  image: /assets/posts/en/domotizing-our-house-with-home-assistant/header.jpg
tags: [ English, Domotic, Home, Assistant, Hassio, Hass.io ]
categories: [ English, Domotic, Home Assistant ]
lang: en
ref: 12
permalink: /en/domotizing-our-house-with-home-assistant/
---

Let's admit something, we humans are really lazy, but computer scientists are even more so. That's why we try to automate as much as possible, sometimes too much. A very common phrase is:

> If you do something more than twice, automate so you don't have to do it a third time.

We can extrapolate this phrase to our home. How many times have we forgotten a light on? How many times have we lost the remote control? What if we leave the house for a few days and we get robbed?

> Disponible en español [aquí](https://www.nocountryforgeeks.com/domotizando-nuestra-casa-con-home-assistant/).

Solving all these "problems" is more accessible today than ever, but if we start buying "gadgets", we would find a mobile full of third-party applications, and many services in danger of being [spied](https://money.cnn.com/2017/10/11/technology/google-home-mini-security-flaw/index.html) on or that at some point [stop providing that service](https://9to5google.com/2018/11/27/psa-its-not-just-you-nest-service-is-currently-down/), and with no interconnection between all systems.

[**Home Assistant**](https://www.home-assistant.io/) was born with the concept of creating an [*Open Source*](https://wikipedia.org/wiki/Open_Source_Definition) control system that bears the banner of privacy and local service.

## Home Assistant

![Home Assistant](/assets/posts/en/domotizing-our-house-with-home-assistant/home-assistant-mobile.jpg)

[Home Assistant](https://www.home-assistant.io/) is a home automation management software for our home capable of integrating a large number of devices and services, both from third parties and our own. The best thing is that, although slowly evolving, it is a [very lively open source software](https://github.com/home-assistant), with a huge community, powerful and with a very good learning curve.

You can try a *DEMO* [**here**](https://demo.home-assistant.io/#/lovelace/0)

Being a software developed in [*python*](https://www.python.org/) is compatible with a multitude of operating systems and devices. It is distributed in different ways to delight beginner and more advanced users.

## *Hass.io* vs. *Home Assistant* vs. *Docker*

Before we go into matter and since this is usually quite complex. Let's distinguish the most common ways to run **Home Assistant**.

- [**Hass.io**](https://www.home-assistant.io/hassio/installation/) is a *Linux* distribution optimized to run **Home Assistant** on [*Docker*](https://www.docker.com/). That is, a custom distribution has been created so that everything is ready to install and run. As an advantage, it is simple and has a very powerful [**Add-ons**](https://www.home-assistant.io/addons/) system. As a disadvantage, we do not have real access to the operating system running the *Hass.io* container. This is the installation option we will see in this post.

- **Home Assistant** as we have mentioned is a software and as such, can be installed in any *Linux* distribution (even if you dare in *Windows*), the normal thing is to install a simple *Linux* distribution, like [**Hassbian**](https://www.home-assistant.io/docs/hassbian/installation/) for [*Raspberry Pi*](https://www.raspberrypi.org/) for example, and then *Home Assistant* as "application", having all the power and control.

- [**Docker**](https://www.home-assistant.io/docs/installation/docker/) is the most advantageous option for some, but also the most advanced. It consists of creating a [*Docker* container](https://www.docker.com/) with *Home Assistant*, so that we have total control, and at the same time isolated from any *Home Assistant* with the advantages that this entails.

Despite all these differences, you can then combine, for example, install *Home Assistant* with *Docker* and put *Hass.io* on top.

## Installing *Hass.io*

As we have mentioned, let's see how to install **Hass.io**, the reason, has the simplest and fastest installation. You can try *Home Assistant* after a few minutes and as we learn, move on to the other systems.

The recommended way to start, given the quality-price, is to use a [**Raspberry Pi 3 B+**](https://amzn.to/2lBMWFL) with [a **MicroSD** of at least *32Gb*](https://amzn.to/2lBMWFL). Of course, you can use [*a wide variety of hardware*](https://www.home-assistant.io/hassio/installation/), but I think this is one of the best options.

1. Download the latest *Hass.io* image for your device. At the time of writing this article, [**Hass.io 2.12** for *Raspberry Pi 3 B / B+ 32bit*](https://github.com/home-assistant/hassos/releases/download/2.12/hassos_rpi3-2.12.img.gz_rpi3-2.12.img.gz).

2. Download and install on your computer a *SD* card image burner software. I like [**balenaEtcher**](https://www.balena.io/etcher).

3. Burns the image on the *SD* card. To do this, simply open the previously installed program, insert the *SD* card into the reader, select the downloaded image in **step 1** and click *Flash*. Do not remove the card yet.

    ![balenaEtcher](/assets/posts/en/domotizing-our-house-with-home-assistant/balena-etcher.gif)

4. **Optional**, but that I strongly recommend is to create a file to configure the network connection of the device, is a somewhat complicated step, let's go in parts.

    My personal recommendation is to assign a static *IP* to our *Raspberry Pi*. Although *IPs* can be reserved in the *Router*, I think a better option is to create a range of dynamic *IPs* (*DCHP*) from a high number of devices, for example, ``192.168.1.128-255``, so that all *IPs* less than *128* can be used for devices with static *IP*. All this by connecting a ***Ethernet*** cable between the server device and the *Router*.

    1. Open the directory of the newly created *SD* card and create the directories *`CONFIG`* and inside *`network`*. Finally, the file *`my-network`* inside this last one, without extension. This will leave **`X:\CONFIG\network\my-network`**.

    2. Edit the file **`my-network`** and include the following code:

        ```conf
        [connection]
        id=my-network
        uuid=d55162b4-6152-4310-9312-8f4c54d86afa
        type=802-3-ethernet

        [ipv4]
        method=manual
        address=192.1.1.4/24,192.168.1.1
        dns=8.8.8.8;8.8.4.4;

        [ipv6]
        addr-gen-mode=stable-privacy
        method=auto
        ```

        Change the `address` field to the desired *IP* and *gateway*, and the `dns` field to those *DNS* we still use. Write down the *IP* that you are going to assign to the device because it is quite important.

5. Eject the *SD* card from the computer and insert it into the *Raspberry Pi*. Plug it into the power supply and wait, depending on the device the download of the latest version and installation can take **up to 20 minutes**.

    ![Installing Hass.io](/assets/posts/en/domotizing-our-house-with-home-assistant/preparing-hassio.jpg)

6. In the meantime, we can search our *Router* manual and see if it has **mDNS** available, in which case, we will be able to access the *Home Assistant* graphical interface from any other device by accessing the following address with the browser: `http://hassio.local:8123`, otherwise, and since we have configured a static *IP* on our server, we can access the interface with the address: `http://192.168.1.4:8123` (*IP* being the one previously set in the network configuration file).

## Basic configuration

Once installed, a couple of screens will be displayed to perform the basic configuration of *Home Assistant*, such as the user and the location of the device. Finally, log in and the main *Home Assistant* screen will appear.

![Log-in Home Assistant](/assets/posts/en/domotizing-our-house-with-home-assistant/home-assistant-login.jpg)

> Note that during installation, *Home Assistant* will look for smart devices on the network with which it has auto-integration to include them directly in its configuration. Don't worry, we can add them later. In fact, more advanced *Home Assistant* users tend to avoid these automatic integrations.

## Conclusion

![Main page of Home Assistant](/assets/posts/en/domotizing-our-house-with-home-assistant/home-assistant-dashboard.jpg)

At this point, we'll have our own **Home Assistant** running on a local server. And with it, a whole range of possibilities that we will discover in next articles. It took just a few minutes to get started, but soon we'll discover all the potential **Home Assistant** has in store for us.

This and other articles complement the explanation of the [**GitHub** repository](https://github.com/danimart1991/home-assistant-config) where the configuration of the domotization of my house is available.
