---
title: "Improve the look and feel of Home Assistant"
header:
  image: /assets/posts/en/improve-the-look-and-feel-of-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 26
permalink: /en/improve-the-look-and-feel-of-home-assistant/
---

[*Home Assistant*](https://www.home-assistant.io/) has a Web interface based on [*Google Material Design*](https://material.io/). While it is quite nice to look at, for those who want to change the look a little, *Home Assistant* gives us options to change their style.

> Disponible en español [aquí](https://danielmartingonzalez.com/mejorar-el-aspecto-de-home-assistant)

## How do themes work?

Within the *Home Assistant* configuration, modifying the `configuration.yaml` file, there is the [`frontend`](https://www.home-assistant.io/integrations/frontend/) integration; and within it, the [`themes`](https://www.home-assistant.io/integrations/frontend/#defining-themes) node, with which you can create different themes that can be used in the interface simply by selecting them in our user profile.

These **Themes** base their configuration on modifying *CSS* variables, that is, style variables that are used in the Web interface. So, for example, you can create a theme called **Happy**, which changes the variable from the primary color to the pink color.

```yaml
frontend:
  themes:
    happy:
      primary-color: pink
```

To use the newly created theme, simply go to the *Home Assistant* user profile and select the theme.

![Selection of themes in Home Assistant](/assets/posts/en/improve-the-look-and-feel-of-home-assistant/image01.jpg)

## Community Themes

As starting to create a theme from scratch, searching for *CSS* variables and trying to come up with something nice can be very complicated, the community creates and shares themes for anyone to use. Just prepare the *Home Assistant* theme configuration and then include the themes as separate files.

1. In the file `configuration.yaml` put the following lines replacing the lines of the *Happy* theme that has been tested before.

    ```yaml
    frontend:
      themes: !include_dir_merge_named themes/
    ```

2. In the same directory where the `configuration.yaml` file is located, create a folder called `themes`.

    ![List of directories of Themes](/assets/posts/en/improve-the-look-and-feel-of-home-assistant/image02.jpg)

3. Inside you have to create a `THEMENAME.yaml` file for each theme you want to include, in this way, for the previous example, create a `happy.yaml` file with the following content:

    ```yaml
    happy:
      primary-color: pink
    ```

4. Save, restart the server and check that you can still select the *Happy* theme.

5. From the [official community forum](https://community.home-assistant.io/), in the section of [Themes](https://community.home-assistant.io/c/projects/themes), you can **download and share themes** made by the community. They simply have to follow the same structure.

    For example, you can copy/download the [Clear](https://community.home-assistant.io/t/clear-theme/100464) theme. To do this, just create the file `clear.yaml` inside the folder `themes` and include all the content indicated in the forum thread by the author:

    ```yaml
    clear:
      # Background image
      lovelace-background: 'center / cover no-repeat url("/local/day.jpg") fixed'

      # Colors
      text-color: '#636B75'
      text-medium-color: '#8c96a5'
      text-light-color: '#BAC0C6'
      accent-color: '#00a1ff'
      background-color: '#F7F8F9'
      background-color-2: '#F4F5F6'
      background-card-color: 'rgba(255,255,255,1.0)'
      border-color: '#E8E8E8'

      # ...
    ```

    > Some themes include **pictures**, usually with the address `/local/.../IMAGE.jpg`. These images usually come with the theme and must be downloaded and saved in the folder `www` which must also be in the same directory as the file `configuration.yaml` and the folder `themes`. When restarting, *Home Assistant* will move the files following the same directory scheme to the `local` folder.

6. The last step is to **save, restart and test the new themes**.

    ![Tema Clear test](/assets/posts/en/improve-the-look-and-feel-of-home-assistant/image03.jpg)

## Automatically change the theme

One of the most popular functions these days in applications is to change their style between **light and dark** depending on whether it is day or night respectively.

This functionality is easily replicable in *Home Assistant* using an **automatization**. You only need to have at least two themes configured (the default theme can be used) and the entity [`sun`](https://www.home-assistant.io/integrations/sun/) configured. This can be done in two ways:

The first option, is visual. From the menu `Configuration -> Automation` create a new automation.

1. Enter a name for the automation.

2. Enter **3 triggers**, one when *Home Assistant* starts, and two more for Sunrise and Sunset events.

    ![Automation triggers](/assets/posts/en/improve-the-look-and-feel-of-home-assistant/image04.jpg)

3. Enter an action that calls the theme change service (`frontend.set_theme`), but depending on the state of the `sun` entity, assign one theme or another. Indicate the themes you want to use.

    ![Automation action](/assets/posts/en/improve-the-look-and-feel-of-home-assistant/image05.jpg)

4. Save and restart the server. You will already have the automatic change of themes functional.

Another option is to program the automation using the following `YAML` code:

```yaml
- id: change_theme_on_sun_horizon
  alias: Change theme ON sun horizon
  trigger:
    - platform: homeassistant
      event: start
    - platform: state
      entity_id: sun.sun
      to: "above_horizon"
    - platform: state
      entity_id: sun.sun
      to: "below_horizon"
  action:
    - service: frontend.set_theme
      data_template:
        name: >
          {% raw %}{% if states.sun.sun.state == "above_horizon" %}
            clear
          {% else %}
            clear-dark
          {% endif %}{% endraw %}
```

Both generate the same result.

## Conclusion

With this short article we have discovered how to give a makeover to the *Home Assistant* graphical interface and how to automate theme changes. Now you only have to try until you find the theme that best defines your home.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
