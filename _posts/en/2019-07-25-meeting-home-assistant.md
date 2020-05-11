---
title: "Meeting Home Assistant"
header:
  image: /assets/posts/en/meeting-home-assistant/header.jpg
tags: homeassistant hassio domotica
lang: en
ref: 13
permalink: /en/meeting-home-assistant/
---

Once [created our **Home Assistant** server](/en/domotizing-our-house-with-home-assistant/), let's see how its web interface works and how to make the basic settings to start working and enjoy our intelligent home.

To access the web interface, as we saw in the [previous article](/en/domotizing-our-house-with-home-assistant/), simply go to `http://hassio.local:8123` if your router has *mDNS*, or `http://xxx.xxx.xxx.xxx:8123` otherwise, replacing the `xs` with the *IP* you previously configured in the network configuration, and then logging in with the account created during installation.

## *Lovelace*

[Home Assistant](https://www.home-assistant.io/), apart from a web server, displays a web app so that we can visualize all the entities and automations created in a very simplified way. This visual part is called [**Lovelace**](https://www.home-assistant.io/lovelace/).

By default **Lovelace** is configured to "autodiscover" configurations and automations, that means that if we integrate some device through the integrations wizard, or as we will see later through the configuration file, automatically we will be shown cards and information on the main screen (*Summary*).

![Home Assistant Lovelace](/assets/posts/en/meeting-home-assistant/lovelace-dashboard.jpg)

In addition, and accessing from the menu on the left, we will have by default the following features:

- **Map**: A full screen map where we will have available all the devices or people that we have marked to track, as well as different zones that we configure. For example, one area could be our home, and another could be our work office. In this way, in Home Assistant our partner could know if we are coming home or we are in a traffic jam.

- **Logbook**: Página para ver todos los cambios que han ocurrido en nuestras entidades a lo largo del tiempo a modo de log. Se puede filtrar para tener una visión más clara.

- **History**: Page to see all the changes that have happened in our entities over time as a log. It can be filtered to have a clearer vision.

- **Hass.io**: If we have installed *HA (Home Assistant)* through *Hass.io* or we have installed it a posteriori, from this tab we will have available all the options that it offers, such as *Snapshots*, installation and management of *Add-ons*, and *Log* of the system among others.

- **Configuration**: From here we will make all the configuration of *Home Assistant* in a more graphical way. So much users, as integrations, areas, entities customization...

    To add a new integration just access integrations from this menu.

    > More advanced users tend to largely skip this tab and try to configure everything using source code. In future post we will see the reasons.

- **Developer tools**: This is the most advanced part of the menu. It contains entries and options to change the different services, entities, states of the entities,... as well as to create templates and the information of *Home Assistant* as current version, *log*, and system information.

If you don't like *Lovelace*, there are ways to change the themes, add cards and custom components to suit your needs. And if none of this convinces you, there are *dashboard* alternatives such as the [**Add-on Home Panel**](https://github.com/hassio-addons/addon-home-panel).

## Installing our first *Add-on*

To break the ice with *Hass.io*, there's nothing better than to start installing *Add-ons*. These *Add-ons* are improvements that are applied to our web server to give them new features. To access them, simply go to the *Hass.io* tab and then go to the *Add-on Store* option.

![Add-on Store](/assets/posts/en/meeting-home-assistant/hassio-addon-store.jpg)

In this page we will have the *Add-ons* collected from different repositories. At the moment, from the official *Add-ons* repository and from the community's verified *Add-ons* repository. You can add as many repositories as you want, even *Add-ons* own repositories that we are developing.

As a trial and step install a useful *Add-on*. Select the *Add-on* [**Check Home Assistant configuration**](https://github.com/home-assistant/hassio-addons/tree/master/check_config) from the list.

![Check Home Assistant Configuration](/assets/posts/en/meeting-home-assistant/official-addons.jpg)

When a new version of *Hass.io* and/or *Home Assistant* is released, it sometimes comes with *Breaking Changes*, that is to say, with changes that can spoil our configuration and make some things stop working or even that our whole system collapses and doesn't start. The [**Check Home Assistant configuration**](https://github.com/home-assistant/hassio-addons/tree/master/check_config) *Add-on* takes care of reviewing all our configuration, installing the new version in a separate container and proving that there is no problem. It doesn't guarantee 100% that everything is perfect, but at least it gives us an idea that we can at least install and start the server once we upgrade to the new version.

To install, as simple as clicking on **INSTALL**, and once installed, and as indicated in the documentation and for this *Add-on*, click on **START**.

![Installing Check Home Assistant Configuration](/assets/posts/en/meeting-home-assistant/check-home-assistant-configuration-addon.jpg)

It'll take a few minutes. But when it's over, at the bottom, in the **Log**, we'll have a record of what the *Add-on* has done, and whether our system is ready to update. From now on, we just have to hit the *START* button every time we want to test what would happen if we updated *Hass.io*.

![Check Home Assistant Configuration Log](/assets/posts/en/meeting-home-assistant/check-home-assistant-configuration-log.jpg)

> The *Add-on* will stop automatically when finished. Of course, other *Add-ons* don't work this way, they have a permanent functionality and we can even start them with the *Hass.io* startup in such a way that they are always available.

## Creating our first automation

One of the most useful features of Home Assistant is the [**automations**](https://www.home-assistant.io/getting-started/automation/). Again, although they can be created from the dashboard, as we will see now, many users tend to perform these automations using [*yaml*](https://yaml.org/) code and [*python*](https://www.python.org/) *scripts* to facilitate their reuse and backup.

To test how automations work, go to **Configuration - Automations**, and click on the button to create a new one.

![Home Assistant Automations](/assets/posts/en/meeting-home-assistant/automation.jpg)

Set the following parameters:

- **Name**: HA Started.
- [**Triggers**](https://www.home-assistant.io/docs/automation/trigger/):
  - **Type**: Home Assistant
  - **Event**: Start
- [**Conditions**](https://www.home-assistant.io/docs/automation/condition/): Skip this part.
- [**Actions**](https://www.home-assistant.io/docs/automation/action/):
  - **Type**: Call Service
  - **Service**: `persistent_notification.create`
  - **Service Data**:

    ```json
    {
      "title": "Home Assistant",
      "message": "Home Assistant started."
    }
    ```

Save.

![Home Assistant Automation](/assets/posts/en/meeting-home-assistant/new-automation.jpg)

The **automation** created triggers a notification for all users on the *dashboard* when *Home Assistant* starts. To test it, go to the **Settings -> General** tab and click **Restart** at the bottom. After waiting a few minutes, *Home Assistant* will have restarted. Go back to the main page (you can click on the **Summary** tab), and you will see a notification at the top right.

![Home Assistant nueva notificación](/assets/posts/en/meeting-home-assistant/new-notification.jpg)

Clicking will display the **notification** that has just been sent with our automation.

![Home Assistant mostrar notificación](/assets/posts/en/meeting-home-assistant/notification.jpg)

## Conclusion

Now you know the basics to start playing *Home Assistant*. My recommendation is that you start playing, create integrations, configurations, automations and discover all the potential that *Home Assistant* hides. In a few minutes you'll be eager to make your whole home smart.

This and other articles complement the documentation of the [**GitHub repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
