---
title: "The meta-component Default Config"
header:
  image: /assets/posts/en/the-meta-component-default-config/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 23
permalink: /en/the-meta-component-default-config/
---

Reviewing and cleaning the `configuration.yaml` file that [we have had to edit on other occasions](/en/configure-home-assistant-editing-its-files/). I've noticed the `default_config` component. A component that comes by default activated and so far had not paid much attention.

The [**Default Config**](https://www.home-assistant.io/integrations/default_config/) component is a meta-component, which means that when you use it, it configures many other [**Home Assistant**](https://www.home-assistant.io/) components by default. Removing it causes these components to stop working and those we want to use, we have to specify them one by one. Why would we want to do this? To improve the performance of our server by removing components and functionality that we are not using.

Let's analyze the components included in `default_config` and include them in our `configuration.yaml` file as needed.

- **`automation`**: Enables the [Automations](https://www.home-assistant.io/integrations/automation/). In most cases, this integration is replaced by the line `automation: !include automations.yaml`, which will group all the automations in the `automations.yaml` file. If we are not going to use automations in *Home Assistant*, all this code can be deleted.
- **`cloud`**: Enables [*Home Assistant Cloud*](https://www.home-assistant.io/integrations/cloud/) that allows remote access to *Home Assistant*, cloud configuration storage, or integration of services such as *Amazon Alexa* or *Google Assistant* paying a subscription.
- **`config`**: Enables the section [Configuration](https://www.home-assistant.io/integrations/config/) in the visual part (*frontend*) of *Home Assistant*. From it we can visually configure integrations, users, areas, automations...
- **`frontend`**: Enables [the *Home Assistant* visual environment](https://www.home-assistant.io/integrations/frontend/). Can be disabled if you want to use other visual environments (*dashboard*). This integration also includes several interesting parameters such as loading *HTML*, *CSS* or *JavaScript* code, or being able to add different themes to change colors and style.

  ```yaml
  frontend:
    themes:
      happy:
        primary-color: pink
      sad:
        primary-color: blue
  ```

  Once defined, they can be selected from user settings. The community itself has a [large list of themes to use](https://community.home-assistant.io/c/projects/themes).
- **`history`**: Enables [historical log](https://www.home-assistant.io/integrations/history/). Based on the `recorder` component configuration keeps a record of all components and entities that are not `hidden`. It also allows configuring entities to be excluded or included in the history. The whole record can be viewed from the **History** tab.
- **`logbook`**: Enables the [display of the historical log](https://www.home-assistant.io/integrations/logbook/) (just show it), in reverse chronological mode; a list of the changes that have occurred in *Home Assistant* over time. Again, entities can be included or excluded. It can be viewed from the **Logbook** tab.
- **`map`**: Enables the [map](https://www.home-assistant.io/integrations/map/) that shows the devices tracked by *Home Assistant*. It can be viewed from the **Map** tab.
- **`mobile_app`**: Enables integration of [mobile applications](https://www.home-assistant.io/integrations/mobile_app/) with *Home Assistant*. If you are not going to use external applications that integrate *Home Assistant*, you can disable or delete this integration.
- **`person`**: Enables integration of [people](https://www.home-assistant.io/integrations/person/) with their `device_tracker` entities using *YAML*. That is, you can configure people with their tracking entities using the visual editor, or use the file `configuration.yaml`.

  ```yaml
  person:
    - name: Dani
      id: dani1234
      user_id: 12345678912345678912345678912345
      device_trackers:
        - device_tracker.dani_phone
  ```

- **`script`**: Enables the use of [*Scripts*](https://www.home-assistant.io/integrations/script/). As with Automations, it is normal to use the line `script: !include scripts.yaml` and define the *scripts* through *YAML* in the file itself or visually through the **Configuration** section.
- **`ssdp`**: Enables [*SSDP* (*Simple Service Discovery Protocol*)](https://www.home-assistant.io/integrations/ssdp/). This integration searches the network for devices and services to automatically add them to the **Integrations** section and we don't have to configure them manually.
- **`sun`**: Enables the [*Sun*](https://www.home-assistant.io/integrations/sun/) entity that simulates the sun's position based on the current position. Automatically obtains the configuration of the data indicated in the **General Configuration** of our server. It is highly recommended to indicate the elevation so that the data are accurate.
- **`system_health`**: Enables the *API* that offers the information of different [diagnostics of the system](https://www.home-assistant.io/integrations/system_health/) and its components. It can be consulted from the **Tools for Developers** section, **Info** tab.
- **`updater`**: Enables the sensor to check daily for [available system updates](https://www.home-assistant.io/integrations/updater/). By default it collects certain data (none sensitive) to be sent to *Home Assistant* servers to improve the service.
  > Although it is indicated that it does not make much sense to use this sensor in [*Hass.io*](https://www.home-assistant.io/hassio/) because it already has an update warning system, it is necessary if you want to show a sensor that notifies us visually.
- **`zeroconf`**: Enables the [*Zeroconf*/*Avahi*/*Bonjour*](https://www.home-assistant.io/integrations/zeroconf/) component that searches the network for services and devices related to *Zeroconf* and automatically adds them to the **Integrations** section.

Finally, remember to delete and modify the entities you no longer use. For example, if you include the entity `person` manually in the `configuration.yaml` file, you have to remember to delete the old entity in the **Configuration -> Persons** section, and in the **Configuration -> Entity Registry** section and if you have called it with the same `id`, modify the new entity so that it has the `id` you want.

## Conclusion

Once added the integrations that interest us and carefully delete only those that we are sure we will not use, we have improved the performance of our server *Home Assistant* and know a little more about the platform.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
