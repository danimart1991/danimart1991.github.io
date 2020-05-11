---
title: "Home Assistant configuration in YAML"
header:
  image: /assets/posts/en/home-assistant-configuration-in-yaml/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 27
permalink: /en/home-assistant-configuration-in-yaml/
---

Although version by version of [*Home Assistant*](https://www.home-assistant.io/) there is a growing tendency for everything to be integrated in a visual way and therefore any user without programming knowledge can use and [configure *Home Assistant*](/en/domotizing-our-house-with-home-assistant/), the reality is that even today it is necessary to touch certain files to configure some of the most used parameters and to have advanced control of the system.

My recommendation is to try to move everything to manual *YAML* configuration files when possible. We ensure more centralized management, being able to back up simply by copying the file or its content, and being able to share settings with other users easily and securely.

As a counterpoint, in many cases the option to perform visual configuration of the parts that are passed to *YAML* is lost.

## Required knowledge

The language used to perform most configurations is *YAML*. The first step is to learn how to use the language, and there is nothing better than the [official specification sheet](https://yaml.org/spec/1.2/spec.html) for this.

On the other hand, we need a method to edit the *YAML* files that are on our server. In the article ["configure Home Assistant editing its files"](/en/configure-home-assistant-editing-its-files/) you can see how to install the [*Add-on Configurator*](https://www.home-assistant.io/addons/configurator/) and how to start editing files.

> In more advanced environments, it will be necessary to learn languages like [*Jinja2*](https://palletsprojects.com/p/jinja/) for the realization of *Templates* or [*JSON*](https://json.org/) for certain external configurations.

## Configurations to YAML

Entering a slightly more swampy terrain, we find different types of parameters, different places where these parameters are saved and various methods of passing all *Home Assistant* settings to *YAML* files. In parts, we will see how the different options that we have already been able to edit visually behave:

- **Automations**. The [automations](https://www.home-assistant.io/integrations/automation/) are saved directly to the root folder in the `automations.yaml` file. It can be edited to create our automations or use the visual editor. The difference is that by editing the file you can [create much more powerful automations](https://www.home-assistant.io/docs/automation/templating/), however, they cannot be edited later in the visual mode. You need to include the following line in `configuration.yaml`.

  ```yaml
  automation: !include automations.yaml
  ```

- ***Scripts***. Similarly, the [*scripts*](https://www.home-assistant.io/integrations/script/) are already saved by default in the file `scripts.yaml` in the root configuration folder, and can be edited. You need to include the following line in `configuration.yaml`.

  ```yaml
  script: !include scripts.yaml
  ```

- **Customizations**. More of the same. `customize.yaml` file. You need to include the following lines in `configuration.yaml`.

  ```yaml
  homeassistant:
    customize: !include customize.yaml
  ```

- **Areas**. It is **NOT** possible to declare them (and almost use them) in any *YAML* file..

- **Integrations**. It depends. Some [integrations](https://www.home-assistant.io/integrations/) are ready to be included in `configuration.yaml`, others need to make use of the *Configuration -> Integrations* section, and there is no way to move them to a *YAML* file. Check the documentation for each integration to see when it can be moved to *YAML* and when it cannot. For example, [*OpenWeatherMap*](https://www.home-assistant.io/integrations/openweathermap/) is an integration that can be easily moved to *YAML*; [*ESPHome*](https://www.home-assistant.io/integrations/esphome/), cannot be moved at this time. My recommendation is to try to configure all integrations via *YAML* that allow this.

- **Persons and Users**.
  - Users can **NOT** be moved to *YAML*, they are system users and can be created from the* Configuration -> Users* section. They are used to identify each individual who can use the system.
  - [*Person*](https://www.home-assistant.io/integrations/person/) are entities that associate each user with different device trackers to indicate their position. These entities can be declared in *Configuration -> Persons* or in the `configuration.yaml` file just like any other entity.

    ```yaml
    person:
      - name: Dani
        id: dani
        user_id: 68fa5522b25e4f719fdfe20a398f508e
        device_trackers:
          []
    ```

- **General Setup**. *Home Assistant* has general information such as the name of the house or its *GPS* position. It can be configured visually from *Configuration -> General*. But also via *YAML* with the following lines within the `homeassistant` node into `configuration.yaml` file.

  ```yaml
  homeassistant:
    name: My Home
    latitude: 40.4167511
    longitude: -3.7036432
    elevation: 500
    unit_system: metric
    time_zone: Europe/Madrid
  ```

  > It is highly recommended to move the latitude, longitude and elevation data to [the `secrets.yaml` file](/en/protect-your-private-info-with-secrets-yaml/).

  You can check *GPS* positions and elevations on websites like [CalcMaps](https://www.calcmaps.com/map-elevation/).

- ***Lovelace***. Moving all *dashboard* configuration to *YAML* is not complicated, but once moved, all interface modifications will always have to be done in *YAML*. Therefore, we are going to leave the whole *Lovelace* part for another article, so that we can extensively develop the different interface editing modes and their advantages and disadvantages.

## Conclusion

By moving all the options to *YAML* you get a better organization of all the configurations of our system, as well as an ease of backup, and speaking of security. Now you can use the `secrets.yaml` file and have your sensitive data located in one file, allowing you to share your settings without worrying.

We only need to move the *Lovelace* part, which will be seen in another article.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
