---
title: "Control smart devices with Tuya Smart Life"
header:
  image: /assets/posts/en/control-smart-devices-with-tuya-smart-life/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 21
permalink: /en/control-smart-devices-with-tuya-smart-life/
---

Tuya is a company dedicated to the [Internet of Things (IoT)](https://en.wikipedia.org/wiki/Internet_of_things). It has a multitude of products, applications, services... creating its own complete ecosystem.

The brand explosion came when the electronic schemes and their ecosystem were public released so that any company could develop its own devices based on the ones *Tuya* had previously created, and unify all of them in the same platform. Consumers gained by simply having to install one application and with lower prices due to competition, and companies could develop smart products faster, easier and cheaper.

![Tuya ecosystem](/assets/posts/en/control-smart-devices-with-tuya-smart-life/image01.jpg)

All this has the effect that if you have bought a smart product (especially Chinese brands), it is more likely that you use the application **Tuya Smart Life** (or a similar one). Its use depends on the device, but in most cases, it is very easy to add and use a new device. As an added value, it is compatible with [*Google Assistant*](https://assistant.google.com/) and [*Alexa*](https://es.wikipedia.org/wiki/Amazon_Alexa).

## Experience

Among other *Tuya* devices, I have a *Blitzwolf SHP2* smart plug that offers to turn on and off from the application, a physical button, and consumption meter. Adding it to the application was as simple as plugging it, tap the "add device" button in the application and waiting.

![Plug configured with Tuya Smart Life](/assets/posts/en/control-smart-devices-with-tuya-smart-life/image02.jpg)

Pros:

- The application is really fast, reliable, powerful and quite well designed.
- We will be able to create timers, scenes and automations.
- In this model, the physical button makes it possible to turn the device on and off both physically and from the mobile. This tends to be a problem in intelligent devices because always need current, the physical switch of our appliance is useless and if the smart device not have a switch, we could only turn on and off from the mobile.
  > This problem mainly happens in smart bulbs that force us to always leave turned on the wall switch and control it exclusively from a mobile application. That's why I don't recommend them.
- If we desired, it is compatible with [*Home Assistant*](https://www.home-assistant.io/).

Cons:

- We are forced to make all our devices compatible with *Tuya* in order to avoid having more than one application installed on our mobile.
- For any action, on most *Tuya* devices, we are required to make use of their servers. This means that, when you turn on a device from your mobile, a request is sent to their servers, and the servers send a signal to the socket to be turned on. This gives problems of availability if the server is down, or latency if the server takes time to perform the action. (In another article we will see how this can be avoided).

## Usage with *Home Assistant*

> At the time of publication of this article, [*Home Assistant* gives support for quite a few devices and functionalities of *Tuya*](https://www.home-assistant.io/integrations/#search/tuya) such as switches, plugs, scenes... however other functionalities such as consumption meter are not available. On the other hand, using Home Assistant integration does not override the official application, so both can be used.

The use of the *Tuya* platform in *Home Assistant* is quite simple. We only need the **country code** that we indicated when we registered in the application, as well as the **username** and **password**. All this data is added to the file `configuration.yaml` as [we have done on other occasions](/en/configure-home-assistant-editing-its-files/) with the following code:

```yaml
tuya:
  username: USER
  password: PASSWORD
  country_code: COUNTRY_CODE #country_code: 34 for spain
  platform: smart_life
```

Since there are several applications available for *Tuya* devices, in the `platform` node we must indicate in which [we have been registered](https://www.home-assistant.io/components/tuya/#platform).

Restart your *Home Assistant* server, and depending on how you have configured [*Lovelace*](https://www.home-assistant.io/lovelace/), you will have available the different devices and scenes previously configured in the *Tuya Smart Life* application in your Home Assistant interface.

![Plug configured with Tuya in Home Assistant](/assets/posts/en/control-smart-devices-with-tuya-smart-life/image03.jpg)

## Conclusion

We have seen how the *Tuya* devices can be easily configured using a single application and with the addition of being an ecosystem compatible with *Home Assistant*, which makes them quite eye-catching devices and with an affordable price to any budget.

In next articles we will also see how to avoid using *Tuya's* servers by installing a custom *firmware*. Many of these devices are based on the [*esp8266 chip*](/en/meet-esp8266-nodemcu-the-iot-wifi-module/) that can be programmed to take advantage of all its functionalities.
