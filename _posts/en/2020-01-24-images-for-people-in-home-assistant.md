---
title: "Images for people in Home Assistant"
header:
  image: /assets/posts/en/images-for-people-in-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 28
permalink: /en/images-for-people-in-home-assistant/
---

[In the previous article, a distinction was made between user and person](/en/home-assistant-configuration-in-yaml/). By default, one [person](https://www.home-assistant.io/integrations/person/) is usually created for each user. And it is used to [track]((https://www.home-assistant.io/integrations/device_tracker/)) the user and create automations for example to turn off the heating when all the people are out of the house.

If you have not changed the [*Lovelace*](https://www.home-assistant.io/lovelace/) settings, you will see a person indicator as a [*badge*](https://www.home-assistant.io/lovelace/views/#badges) at the top of the home page. If you have set up a *tracker*, you will also have information about where the person is located.

![Home Assistant Dashboard with Persona as Badge](/assets/posts/en/images-for-people-in-home-assistant/dashboard-person.jpg)

However, this icon is always the same for everyone and often looks ugly and difficult to distinguish. The good thing is that it can be changed to an image so that it is easily identifiable. There is no obvious way to associate an image with a person. But there's a little trick to being able to change it from the **Personalization** menu or from `customize.yaml`.

## Personalizing people

![Person visual mode Customize](/assets/posts/en/images-for-people-in-home-assistant/customize-person.jpg)

From the menu **Settings -> Customizations** select the person you want to assign an icon or image to. The attributes to be changed are [`icon`](https://www.home-assistant.io/docs/configuration/customizing-devices/#icon) or [`entity_picture`](https://www.home-assistant.io/docs/configuration/customizing-devices/#entity_picture). The last one will not be displayed in the default menu, you need to select `Other` and put the attribute name and value.

In the case of `icon`, the value will be `mdi:` followed by the chosen icon. You can consult a list of icons in pages like [Material Design Icons](http://materialdesignicons.com/). It will look something like `mdi:alien`.

If you want to use an image, you have to use the `entity_picture` attribute, you can use a web address or upload an image to the `www` folder in the same directory where the `configuration.yaml` file is located. For *Home Assistant* to be able to use the file we will have to change the `www` address to `local` (even though the image is actually in `www`).

![Person image file in folder www](/assets/posts/en/images-for-people-in-home-assistant/configurator-photo-file.jpg)

Save the changes and the change will be available.

## Customize by code

If you prefer to make the change by *YAML* code. In the file `customize.yaml` (p[reviously declared in `configuration.yaml`](https://www.home-assistant.io/docs/configuration/customizing-devices/#manual-customization)) use one of the following lines of code.

- To change the icon:

  ```yaml
  person.dani:
    icon: mdi:alien
  ```

- To change the image (by pre-uploading the image to `www`):

  ```yaml
  person.dani:
    entity_picture: "/local/dani.jpg"
  ```

Reboot the server and that's it.

![Home Assistant Dashboard with Person with changed image](/assets/posts/en/images-for-people-in-home-assistant/dashboard-person-changed.jpg)

## Conclusion

With these simple steps the person entity has been configured to give it a more visual aspect and to be easily distinguishable. These steps can be used with almost any entity to improve the appearance of the *Home Assistant* server interface.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
