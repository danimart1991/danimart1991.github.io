---
title: "Protect your private info with secrets.yaml"
header:
  image: /assets/posts/en/protect-your-private-info-with-secrets-yaml/header.jpg
tags: homeassistant hassio domotica
lang: en
ref: 17
permalink: /en/protect-your-private-info-with-secrets-yaml/
---

When we work with [*Home Assistant*](/en/domotizing-our-house-with-home-assistant/) files, we tend to introduce private keys and data that in the wrong hands can become a **very dangerous and serious problem**.

If you are also like me, that I have public [all my configuration](https://github.com/danimart1991/home-assistant-config), or assiduously you send pieces to other people to help them or to show them part of that configuration, you will have seen yourself erasing your private information before all; with the worry of forgetting to erase some important data.

## `secrets.yaml`

*Home Assistant* offers as a solution a special file called `secrets.yaml` which is in the root of our configuration (in the same folder as `configuration.yaml`). If it is not created, just create a new file with this name.

![config directory](/assets/posts/en/protect-your-private-info-with-secrets-yaml/image01.jpg)

This file has a very simple structure. It's a **keys dictionary** that will then be used in other configuration files. In this way, we will have all the keys organized and centralized in a single file.

In the other files it will be enough to use the variable just created by using `!secret VARIABLE`.

## Example

I'm going to give a simple but quite clear example using one of the configurations seen in [a previous article](/en/configure-home-assistant-editing-its-files/).

The file `configuration.yaml` has the following code:

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: 123456789
```

You can see that the `api_key` node has a private key that no other person should see. Let's hide it.

In the `secrets.yaml` file, we include the next node:

```yaml
weather_openweathermap_api_key: 123456789
```

To use it, just change the `configuration.yaml` node as follows:

```yaml
weather:
  - platform: openweathermap
    name: home
    api_key: !secret weather_openweathermap_api_key
```

Something as simple and effective as this change can be applied to any key or data we want to protect.

## Conclusion

With the use of `secrets.yaml` we will no longer be afraid to share our configuration, and a previous revision will not be necessary, because all our keys and private data will be located in the `secrets.yaml` file. File that I recommend not to share with any other person.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
