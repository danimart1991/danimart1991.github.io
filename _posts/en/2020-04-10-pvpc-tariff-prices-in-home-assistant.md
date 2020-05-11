---
title: "PVPC tariff prices in Home Assistant"
header:
  image: /assets/posts/en/pvpc-tariff-prices-in-home-assistant/header.jpg
categories:
  - IoT
  - Home Assistant
lang: en
ref: 32
permalink: /en/pvpc-tariff-prices-in-home-assistant/
---

As of the date of publication of this article, just one month ago I changed my electricity rate to [Hourly Pricing of Electricity (*PVPC*)](https://www.esios.ree.es/es/pvpc), which is the spanish government-regulated hourly electricity rate.

This rate is characterized by offering a different price for light for each hour of the day. Thus, the consumer will have 24 light prices for each day.

As if this were not enough, you have at your disposal several tolls, that is, periods in which prices change drastically during certain hours of the day. It is therefore important to know the price of light in each hour in order to get the maximum benefit from the *PVPC* tariffs.

<iframe src="https://www.esios.ree.es/en/embed/active-energy-invoicing-price-pvpc" width="100%" height="608"></iframe>

## Configuration

Luckily for us, [Eugenio Panadero](https://github.com/azogue) has created an integration to give us all the information and to be able to integrate it in a very simple way in [*Home Assistant*](https://www.home-assistant.io/).

The [integration](https://www.home-assistant.io/integrations/pvpc_hourly_pricing/) is available natively from version [**0.108** of *Home Assistant*](https://www.home-assistant.io/blog/2020/04/08/release-108/). If you have a smaller version, [a *custom component* is available for its installation](https://github.com/azogue/hassio_config/tree/master/config/custom_components/pvpc_hourly_pricing).

### Visual configuration

The easiest way to configure the integration is from the *Configuration -> Integrations* menu of *Home Assistant*. We add a new integration and search for `pvpc`.

![Visual integration](/assets/posts/en/pvpc-tariff-prices-in-home-assistant/pvpc-visual-integration.jpg)

Next, indicate a name and the type of rate contracted. Save.

![Tariff selection](/assets/posts/en/pvpc-tariff-prices-in-home-assistant/pvpc-tariff-selection.jpg)

You will have available a sensor with the name you indicated in the previous step with all the information.

![Integration example](/assets/posts/en/pvpc-tariff-prices-in-home-assistant/pvpc-integration-example.jpg)

> You can configure up to 3 sensors, one for each available rate.

### Advanced settings

As usual with *Home Assistant* integrations, it can also be configured by modifying the `configuration.yaml` file and adding the following lines of code:

```yaml
pvpc_hourly_pricing:
  - name: "PVPC 2.0 DHA"
    tariff: discrimination
```

The `tariff` field admits `normal`, `discrimination` and `electric_car` according to whether we have contracted 1, 2 or 3 periods respectively.

## Dashboard

Although the integration brings quite a lot of data regarding *PVPC*, when showing it in a *Dashboard* with [*Lovelace*](https://www.home-assistant.io/lovelace/) we see that there is no card that can visually display the full daily or next day's price.

That's why I've created a *Custom Card* to be able to see more or less in our *Dashboards* the same graphic that we have available in the [System Operator Information System](https://www.esios.ree.es/es/pvpc) website.

[**In this other article you can see how to install and configure the card.**](/en/installing-lovelace-plugins/)

![Card example](/assets/posts/en/pvpc-tariff-prices-in-home-assistant/card-example.jpg)

## Conclusion

In a simple way, the *PVPC* (Voluntary Price to the Small Consumer) integration has been configured and a personalized card where you can see all the available data in a very visual way.

Now all that remains is to try to use the appliances that consume the most in the cheapest hours of the day and enjoy the benefits of these types of rates.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
