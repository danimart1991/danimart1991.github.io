---
title: "YAML files by package in Home Assistant"
header:
  image: /assets/posts/en/yaml-files-by-package-in-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 30
permalink: /en/yaml-files-by-package-in-home-assistant/
---

As time goes by and more integrations are added to a [***Home Assistant***](https://www.home-assistant.io/) server, you can see how the configuration files become more complicated and big, and even if you try to [split them into different files according to the type of integration](https://www.home-assistant.io/docs/configuration/splitting_configuration/), each new device modifies several files and any update can be a real headache. It is necessary to give a twist to the organization of the *YAML* configuration files with the help of the [***Packages***](https://www.home-assistant.io/docs/configuration/packages/).

## Packages

The idea behind the *Packages* is to group the integrations of "things" that are related to each other, being able in this case to repeat types of integration between the different packages (as opposed to other alternatives). In addition, you can take advantage of everything you have already learned, such as the `!include` definition.

In a simple way, you can distribute the integrations related to the same topic in one or several files and improve the maintenance and reading of these integrations. You can even share them with other people without having to make many changes.

You can also include customizations, scripts, automations...

Here is an example dividing different integrations by rooms using *packages*:

```yaml
######################
# configuration.yaml #
######################

homeassistant:
  ...
  packages:
    - room1: !include room_1.yaml
    - room2: !include room_2.yaml
```

```yaml
###############
# room_1.yaml #
###############

binary_sensor:
  - platform: template
    sensors:
      temp_room1:
        ...
light:
  - platform: hue
    ...
climate:
  - platform: generic_thermostat
    name: "Room Thermostat 1"
    ...
```

```yaml
###############
# room_2.yaml #
###############

binary_sensor:
  - platform: template
    sensors:
      temp_room2:
        ...
light:
  - platform: hue
    ...
automation:
  - id: turnon_light
    ...
```

> It should be noted that although *Packages* are grouped in a similar way to the [*Group*](https://www.home-assistant.io/integrations/group/) integration, their use is relegated to the organization of the configuration. *Home Assistant* will only use the content found within each *package*.

## Packages folder

![Carpeta Packages](/assets/posts/en/yaml-files-by-package-in-home-assistant/packages-folder.jpg)

You can even go a step further and create a folder in the root of your configuration called *packages* where you can save each of the *YAML* files you create, and instead of declaring them one by one in the `configuration.yaml` file, you can write the next line:

```yaml
homeassistant:
  ...
  packages: !include_dir_named packages
...
```

An example of a configuration following this pattern can be seen in [the repository with my server configuration]((https://github.com/danimart1991/home-assistant-config)).

Now you only must create each of the files and include all the integrations, automations, scripts... that you want to group.

## Conclusion

As the number of configuration lines grows, it is time to reorganize everything a little. ***Packages*** offers a much more sustainable and scalable alternative.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
