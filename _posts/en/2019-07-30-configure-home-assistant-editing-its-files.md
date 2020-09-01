---
title: "Configure Home Assistant editing its files"
header:
  image: /assets/posts/en/configure-home-assistant-editing-its-files/header.png
tags: homeassistant hassio domotica
lang: en
ref: 14
permalink: /en/configure-home-assistant-editing-its-files/
last_modified_at: 2020-09-01
---

While many [*Home Assistant*](https://www.home-assistant.io) configurations and integrations can be made and configured without the need to edit files of any kind, others require at least a few lines of code to function properly.

Therefore, it is good to have and know some tool that allows you to modify the files. One of the simplest and most powerful is the [**File Editor Add-on**](https://www.home-assistant.io/addons/configurator) for [*Supervisor*](https://www.home-assistant.io/hassio/).

## Installation

Go to the menu **Supervisor** ➡ [**Add-on Store**](https://www.home-assistant.io/addons) and look for [**File Editor**](https://www.home-assistant.io/addons/configurator).

![Add-on Store](/assets/posts/en/configure-home-assistant-editing-its-files/hassio-addon-store.png)

It does not require configuration if we do not want to make an advanced use, for the moment, simply click on ***Install***.

Once finished, we need to make sure that the **start on boot** and **show in sidebar** options are active.

![File Editor settings](/assets/posts/en/configure-home-assistant-editing-its-files/fileeditor-addon.png)

Restart the *Home Assistant* server from **Configuration** ➡ **Server Controls** ➡ **Server management** ➡ **Restart**

![Restart HASS](/assets/posts/en/configure-home-assistant-editing-its-files/restart-hassio.png)

## Using *File Editor*

The **File Editor** button will appear on the sidebar, selecting it will open a new page with an editor, a snippet selector, and the configuration, search and file explorer buttons.

![File Editor](/assets/posts/en/configure-home-assistant-editing-its-files/fileeditor.png)

If we click on the **file explorer**, we can open a configuration file and edit it from the editor. By default, the file explorer opens in the `/config/` folder where the *Home Assistant* configuration is located.

Find the file `configuration.yaml` and click, it will open in the editor.

![File Editor open configuration.yaml](/assets/posts/en/configure-home-assistant-editing-its-files/fileeditor-folders.png)

> Before starting, it should be noted that changing certain configuration files causes automatic methods in the *Home Assistant* such as the [*Lovelace*](https://www.home-assistant.io/lovelace/) interface or certain parameters in the configuration tab to be disabled. More advanced users prefer to use configuration files, as they gain more control and simplicity for backing up, as well as being a very convenient way to share configurations with the community.

In our first approximation, we are going to add a `weather` entity that refers to [**OpenWeatherMap**](https://openweathermap.org). By default, *Home Assistant* brings a `weather` entity that makes use of the [*NRK (Norwegian Institute of Meteorology)*](https://www.home-assistant.io/components/met/), which doesn't work very well for locations outside Norway.

The first thing you should do is go to the [*OpenWeatherMap*](https://openweathermap.org/price) website and ask for a *API key* that we will use in the configuration of our entity. Register and access the free plan that the *API key* will give us.

![OpenWeather API key](/assets/posts/en/configure-home-assistant-editing-its-files/openweather.png)

With the editor open, in the file `configuration.yaml`, paste the following code; changing `YOUR_OPENWEATHERMAP_API_KEY` by the *API key* you just got.

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: YOUR_OPENWEATHERMAP_API_KEY
```

The order of each entity created in this file is to your liking, you will be accommodating it and creating groupings as you go including more lines. What is important is the spacing, since [`yaml`](https://yaml.org/) is a language that takes into account the indent of the code.

Once you have edited the file, click on **Save**.

![Save configuration.yaml](/assets/posts/en/configure-home-assistant-editing-its-files/save-configuration-yaml.png)

To reload the configuration, it is necessary to restart *Home Assistant*, a simple way, without having to go to the *Configuration* tab of the sidebar, is to click on the ***File Editor*** settings and click on ***Restart HASS***.

> This menu offers a multitude of interesting options such as listing icons, listing compatible components with *Home Assistant*, *Shell* command console...

![Restart HASS](/assets/posts/en/configure-home-assistant-editing-its-files/restart-hass-configurator.png)

Wait a few minutes, as [*Lovelace*](https://www.home-assistant.io/lovelace/) is in automatic mode, when you enter the *Overview* tab, you should see the [**OpenWeatherMap**](https://www.home-assistant.io/lovelace/weather-forecast/) card with your home location information (can be changed from the *Home Assistant* configuration menu).

![Home Assistant Overview tab](/assets/posts/en/configure-home-assistant-editing-its-files/home-assistant-lovelace-dashboard.png)

> To delete the entity [***met.no***](https://www.home-assistant.io/components/met/), since we are no longer interested, and as it has been registered by a [visual integration](https://www.home-assistant.io/getting-started/onboarding/), go to the *Configuration* menu ➡ *Home Assistant Integrations*, select the integration and click on delete. Restart again, and only the [**OpenWeatherMap**](https://www.home-assistant.io/lovelace/weather-forecast/) card should appear.

This and other articles complement the documentation of the [**GitHub repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
