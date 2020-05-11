---
title: "Track devices in Home Assistant"
header:
  image: /assets/posts/en/track-devices-in-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 29
permalink: /en/track-devices-in-home-assistant/
---

[**Home Assistant**](https://www.home-assistant.io/) has a type of integrations called [**Device Tracker**](https://www.home-assistant.io/integrations/device_tracker/), these integrations are intended to control the location of the different devices that we linked to our server.

Depending on the integration, this "location" can change completely. To give some examples, you can get the *GPS* position of a device, indicate which devices are connected to the *Internet* using the home *Wi-Fi* network, a list of those visible by a *Bluetooth* driver connected to the server...

## Types of tracking according to use

In general, the types of use that are usually given to this type of integration are the following:

- **Outdoor**: Tracking information for an out-of-home device. Normally using the *GPS* tracker that the device has, its position is sent. *Home Assistant* has a map to see where it is.
- **Indoor**: Tracking information of a device inside the house. Useful for example when you want to know in which room you can find a device.
- **Binary sensor**: Informs only if the device is inside or outside the house. Very used to know when a device that is in a fixed position inside the house is turned on (at home) or turned off (outside the house).

## Testing integrations

Given the high number of device tracking integrations (presence detection) available in *Home Assistant*. The best thing to do is to test the integrations we have available from the [***Presence Detection***](https://www.home-assistant.io/integrations/#presence-detection) category.

On my side I have made tests with different integrations trying to guide their use according to the convenience of the device.

![Device Tracker Integrations Testing](/assets/posts/en/track-devices-in-home-assistant/device-tracker-testing.jpg)

### Bluetooth Tracker

Both the [*Bluetooth Tracker*](https://www.home-assistant.io/integrations/bluetooth_tracker/) and its improved variant [*Bluetooth LE Tracker*](https://www.home-assistant.io/integrations/bluetooth_le_tracker/) are quite simple to install, just declare them as a platform in the `configuration.yaml` file.

```yaml
device_tracker:
  - platform: bluetooth_le_tracker
```

They use the *Bluetooth* protocol if available on your server to locate devices within range. Therefore, its use will be of an indoor type. When the server starts it will detect new devices and add them to the file `known_devices.yaml`, there is no way to limit it, so if the range of action is near a busy street, in a short time you will have the server storage full of devices you are not interested in.

As a plus, the accuracy is quite high and since it's a passive detection, the devices do not usually "hide" from the radar. In addition, in other systems such as [*ESPHome*](https://esphome.io/), detection devices can be placed in each room so that we know always the room where the device is located and not only that; from *ESPHome* we can track only those devices that we want and no scanning of foreign devices is done.

### Life360

[*Life360*](https://www.home-assistant.io/integrations/life360/) is an outdoor mobile device location service. It offers a fairly comprehensive application with user registration and the ability to share locations and other data with other users. It is very easy to use and allows its integration in a visual way (`Configuration -> Integrations`) or through *YAML*.

By contrast, as a third party service, location data is recorded on *Life360*'s servers.

### OwnTracks

[*OwnTracks*](https://www.home-assistant.io/integrations/owntracks/) is the *Open Source* alternative to *Life360*. While it does not have as many features as its competitor, data is only sent from the mobile applications to a proprietary server, no third party service is used.

*Home Assistant* by declaring (visually or with *YAML*) the *OwnTracks* integration, will create an *OwnTracks Webhook* server to receive the information securely via *HTTP*. You also have the *MQTT* option if you have a dedicated server.

> **@ChimoC** from *Telegram*'s home automation groups, reports a small bug with the integration of *OwnTracks*. When doing the configuration, *Home Assistant* will show a web address to be included in the *OwnTracks* application like: `https://YOUR_USER.duckdns.org/api/webhook/API_KEY` to which it is necessary to add the server port to make it work: `https://YOUR_USER.duckdns.org:8123/api/webhook/API_KEY`.

### Google Maps

The integration with [*Google Maps*](https://www.home-assistant.io/integrations/google_maps/) is based on taking advantage of *Cookies* where the *GPS* positions of a registered device are sent and received to create a location within *Home Assistant*. Its integration is somewhat complicated. It can be very useful for those people who have *Google Maps* installed on their mobile and do not want to install another application.

### iCloud

If you're using the *Apple* ecosystem, integration with [*iCloud*](https://www.home-assistant.io/integrations/icloud/) can be very convenient for tracking these devices. Integration can be done visually or using *YAML*.

On the other hand, at this moment, accounts with a two-factor authentication present problems when being configured.

### Mobile App

If you use the *Home Assistant* mobile application. It has an [integration]((https://www.home-assistant.io/integrations/mobile_app/)) for device tracking. You only need to open the application's settings in the menu and activate the device's location services. It will automatically link to the server and start sending location data.

### Nmap

[*Nmap*](https://www.home-assistant.io/integrations/nmap_tracker/) is a network scanning utility. What you get with this internal type of integration is to see which devices are connected to the network by using an *ARP* scan of the range you specify in the `configuration.yaml` file. Typically, the *netmask* is `255.255.255.0`, which indicates that the range of available *IPs* for the devices is one of the last 254 *IPs*. This means that if, for example, the gateway (Router) *IP* is `192.168.1.1`, the range of *IPs* to look for will be from `192.168.1.2` to `192.168.1.255`. This range is indicated with `192.168.1.1/24` or `192.168.1.1-255`.

```yaml
device_tracker:
  - platform: nmap_tracker
    hosts: 192.168.1.1/24
```

As in many other integrations. Found devices will be saved in `known_devices.yaml` and associated with *Nmap* via their *MAC* address.

### Ping

The [*Ping*](https://www.home-assistant.io/integrations/ping/) integration acts similarly to *Nmap*, but is targeted at devices that cannot be accessed by *ARP*. Another point is that *Ping* does not scan the entire range of *IPs*, but instead provides a list of *IDs* and *IPs* to be tracked using *ICMP*.

```yaml
device_tracker:
  - platform: ping
    hosts:
      my_iphone: 192.168.1.3
```

Likewise, the devices will be added to `known_devices.yaml`.

> In order to use *Ping* integration it is necessary that the device to track has a fixed *IP* assigned.

## Ghost devices

The integrations related to device tracking are *"Best Effort"*, that is, they will try to give the best of themselves and give us accurate data, but they will not always succeed.

So, if for example we configure *Nmap* and *Ping* for a mobile, we'll see that it gives contradictory results.

![Ghost Devices](/assets/posts/en/track-devices-in-home-assistant/ghost-devices.jpg)

This is because some devices get disconnected from *Wi-Fi* networks to save battery power. Others, even if we leave them in *Standby* (off, but with light), remain connected to the network, so the sensors will indicate that it is "on".

The best way to solve this is to use full *Home Assistant* integrations. For example, in the case of *LG* televisions, I use [*webOS TV*](https://www.home-assistant.io/integrations/webostv) integration. They are much more accurate, and also provide more functionality. Another way is to try different integrations until you find the most accurate one for that device.

## Zones and Areas

Once the position of the devices has been obtained, you can assign labels to locations. For example, a zone that indicates the office where you work so that *Home Assistant* knows when you are at work, or assign devices to rooms to have an easier management of the devices in the server.

### Zones

The [**zones**](https://www.home-assistant.io/integrations/zone/) are designed for the first case. From the [Map](https://www.home-assistant.io/integrations/map/) section, or from `Configuration -> Zones`, or from the file `zones.yaml` you can create zones with a *GPS* position and a range. When the device is in that zone, its tracking status will change to the zone identifier.

![Zones Editor](/assets/posts/en/track-devices-in-home-assistant/zones.jpg)

### Areas

The areas work in a similar way to zones, except that they act inside the house. They are usually used to define the different rooms and assign devices to them. They can only be configured visually from `Configuration -> Areas`.

![Areas Editor](/assets/posts/en/track-devices-in-home-assistant/areas.jpg)

## Attaching devices to people

Once the devices are tracked, *Home Assistant* users can be linked by creating [**Person**](https://www.home-assistant.io/integrations/person/) type entities.

These entities have one or more *device_tracker* associated, in such a way that they complement each other and give the user the most precise situation and with it know where he is at all times.

![People Tracking](/assets/posts/en/track-devices-in-home-assistant/people-tracking.jpg)

Simply declare the person in `Configuration -> People` (or via *YAML*) and assign the *device_tracker* type entities you want to use. *Home Assistant* will follow the these rules:

1. If there are stationary trackers (non-*GPS* trackers, i.e., a router or *Bluetooth "device_trackers"*) presenting the status *"home"*, the tracker most recently updated will be used.
2. If there are trackers of type *"gps"*, then the most recently updated tracker will be used.
3. Otherwise, the latest tracker with status *"not_home"* will be used.

In other words. When you're at home, your position is determined first by stationary trackers (if any) and then by *GPS*. When you're outside your home, your position is determined firstly by *GPS* and then by stationary trackers.

> Although having several tracking devices configured increases accuracy, the use of these entities for critical operations is not recommended. A correct use is to turn the heating on or off depending on the position of the person for example, but it is not convenient to use it for alarms or doors until enough checks and tests are done.

## Conclusion

A tour has been made through the device tracking, configuration and problems. All that remains is to analyze all the devices in the house and add them to the system in order to keep better track of the entire household. In addition, it will be possible to link these integrations to people in order to know where they are at any time.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
