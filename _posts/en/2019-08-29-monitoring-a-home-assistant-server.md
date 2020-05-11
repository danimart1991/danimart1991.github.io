---
title: "Monitoring a Home Assistant server"
header:
  image: /assets/posts/en/monitoring-a-home-assistant-server/header.jpg
tags: homeassistant hassio domotica
lang: en
ref: 18
permalink: /en/monitoring-a-home-assistant-server/
---

As our intelligent home grows in number of devices and complexity, but also in usefulness, we must protect the [***Home Assistant*** server](/en/domotizing-our-house-with-home-assistant/) in order to avoid future problems or at least detect them in early.

[*Home Assistant*](https://www.home-assistant.io/) offers several [system monitoring components](https://www.home-assistant.io/components/#system-monitor). For this article, we will use the ***System Monitor*** and [***Speedtest.net***](https://www.speedtest.net/) components to obtain system values and network data respectively.

![System Monitor and Speedtest.net components](/assets/posts/en/monitoring-a-home-assistant-server/image01.jpg)

## Configuring the components

As in [other articles](/en/configure-home-assistant-editing-its-files/), we are going to edit the `configuration.yaml` file to include the [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/) and [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/) components. Open the file and include the following lines of code:

```yaml
speedtestdotnet:
  scan_interval:
    minutes: 30
  monitored_conditions:
    - ping
    - download
    - upload

sensor:
  - platform: systemmonitor
    resources:
      - type: disk_use_percent
        arg: /
      - type: memory_use_percent
      - type: processor_use
      - type: last_boot
```

If you have a sensor node with another platform (e.g. [`miflora`](https://www.home-assistant.io/components/miflora/)), simply add `systemmonitor` to the list:

```yaml
...
sensor:
  - platform: systemmonitor
    ...
  - platform: miflora
    ...
...
```

The [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/) and [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/) components have other very interesting parameters, but for the moment, I am going to put the ones that I consider more interesting or are obligatory for the configuration to work. Check each one's documentation to discover all the available options.

> The [`speedtestdotnet`](https://www.home-assistant.io/components/speedtestdotnet/) component consumes a large *CPU* width (which can be analyzed from the [`systemmonitor`](https://www.home-assistant.io/components/systemmonitor/) component), if you notice outages or performance problems, increases the `scan_interval` parameter to perform the network analysis every several hours.
<br/><br/>
![Speedtest.net card](/assets/posts/en/monitoring-a-home-assistant-server/image02.jpg)

**Reboot the server** from the configuration.

## Visual interface

To show the data on [*Lovelace's Dashboard*](/en/meeting-home-assistant/#lovelace), we're going to use two special cards. These cards cannot be configured visually, so we will edit their code.

For `speedtestdotnet` add a ***History-Graph*** card:

```yaml
entities:
  - sensor.speedtest_download
  - sensor.speedtest_ping
  - sensor.speedtest_upload
type: history-graph
```

![Speedtest.net card](/assets/posts/en/monitoring-a-home-assistant-server/image03.jpg)

For the `systemmonitor` sensor, create a ***Vertical-Stack*** type card:

```yaml
cards:
  - cards:
      - type: gauge
        entity: sensor.processor_use
      - type: gauge
        entity: sensor.disk_use_percent
      - type: gauge
        entity: sensor.memory_use_percent
    type: horizontal-stack
  - entities:
      - sensor.last_boot
    type: entities
type: vertical-stack
```

![System Monitor card](/assets/posts/en/monitoring-a-home-assistant-server/image04.jpg)

## Conclusion

With this information, and other information that may interest you (see documentation of the components), you will be able to monitor your server and predict future problems before they happen in a simple and visual way.

For example, to see if the space available on the server is running out, or if the Internet operator is offering less speed than contracted.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
