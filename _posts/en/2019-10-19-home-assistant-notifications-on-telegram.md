---
title: "Home Assistant notifications on Telegram"
header:
  image: /assets/posts/en/home-assistant-notifications-on-telegram/header.jpg
categories:
  - IoT
  - Home Assistant
  - Telegram
lang: en
ref: 22
permalink: /en/home-assistant-notifications-on-telegram/
---

Once we have configured several devices and entities in [***Home Assistant***](https://www.home-assistant.io/), it's time to receive notifications if they have problems, or if we simply want to know the status of any of them. While we can configure several services for this purpose, I think [***Telegram***](https://telegram.org/) is one of the simplest and most powerful.

## *Bots* on *Telegram*

The way Home Assistant have to communicate with Telegram is through a [***bot***](https://core.telegram.org/bots). That is still an application with which we can communicate to execute different actions. We can create a conversation directly with the bot, or include it in a group to execute these actions or to send us notifications.

To create this *bot*, although there are different ways, a simple one is to use the ***@BotFather*** *bot* to create and configure our own step by step.

1. From *Telegram*, look for ***@BotFather*** and start a conversation with him.

    ![Conversation start with @BotFather](/assets/posts/en/home-assistant-notifications-on-telegram/image01.jpg)

2. Execute the `/newbot` command. He will ask us for a name, which will be the visible name that will appear while we speak with our bot, and a user, who will be the identifier of the bot and should always end with `bot`.

    ![Bot creation with @BotFather](/assets/posts/en/home-assistant-notifications-on-telegram/image02.jpg)

Once completed, we will have created our bot. Next, *@BotFather* indicates the Web address with which we can contact the *bot* and a *token* that we will use later.

> *@BotFather* has many other options to configure and enhance our bot.
  ![Bot configuration options in @BotFather](/assets/posts/en/home-assistant-notifications-on-telegram/image03.jpg)

## Chat / Group for notifications

The next thing is to have a point where you can receive notifications from the *bot*. We can directly start a conversation by searching for the *bot* in the *Telegram* search engine with the user we have indicated before. Or we can create a group with the people we want to contact the *bot* and once group created, add him as one more member.

![Telegram group information with a bot](/assets/posts/en/home-assistant-notifications-on-telegram/image04.jpg)

> **IMPORTANT:** It's very important to add the bot after the group creation. For the next step, it is necessary to start the *bot*. Once the conversation has started or the *bot* was added as a member to a group, send the message `/start`.

Finally, we need to know the identifier of the conversation you just created so that *Home Assistant* knows the conversation to send notifications through the *bot*.

Open the address `https://api.telegram.org/botYOUR_API_TOKEN/getUpdates` in a Web browser by replacing `YOUR_API_TOKEN` with the *Token* @BotFather sent us before (in the example `https://api.telegram.org/bot987654321:ABCDEFGHIJK-LMNOPQRS_TUVWXYZ1234567/getUpdates`).

As a result we will obtain a Web page with a code similar to the following. We must collect the value of the `id` field inside the `chat` node (in the example `chat_id` is `-123456789`).

```json
{
  "ok": true,
  "result": [
    {
      "message_id": 1,
      "date": 1571256851,
      "chat": {
        "id": -123456789,
        "type": "group",
        "title": "Wayne Mansion",
        "all_members_are_administrators": true
      },
      "text": "/start",
      ...
    }
  ]
}
```

## Integration with *Home Assistant*

[As on other occasions](/en/configure-home-assistant-editing-its-files/), to integrate a component into *Home Assistant*, we need to edit the `configuration.yaml` file and add the following code:

```yaml
telegram_bot:
  - platform: polling
    api_key: YOUR_API_TOKEN
    allowed_chat_ids:
      - YOUR_CHAT_ID
```

Substituting `YOUR_API_TOKEN` with the *bot token* that we obtained almost at the beginning of this article, and `YOUR_CHAT_ID` with the chat identifier we just obtained in the previous step.

Thanks to this integration we can [perform several actions on the conversation](https://www.home-assistant.io/integrations/telegram/), but the most useful action is undoubtedly being able to send notifications. These notifications go from sending a simple message, to sending images or buttons that execute actions.

For the moment, we are going to make a simple notification by using an [automation](https://www.home-assistant.io/docs/automation/) that sends us a message when the humidity sensor of the [*Xiaomi Mi Flora* device that we configure some articles ago](/en/caring-your-plants-with-mi-flora-and-home-assistant/) goes at a low level. We have two options:

- By graphic interface. From `Settings > Automations`. We create a new automation with the name that we like the most, and the following configuration:

  ![Creation of Automation for notification in Telegram](/assets/posts/en/home-assistant-notifications-on-telegram/image05.jpg)

- By *YAML*. From the automation file (`automations.yaml`) just include the following code:

  ```yaml
  - alias: 'Telegram Notification My Flora 01 Humidity'
    trigger:
      platform: numeric_state
      entity_id: sensor.living_room_mi_flora_01_moisture
      below: 20
    action:
      service: telegram_bot.send_message
      data:
        message: 'Please water the orchid as soon as possible.'
  ```

**Save**. I recommend to restart the server.

When the humidity of our plant is below 20%, the notification will be launched and will reach the previously created conversation.

![Notification test on Telegram](/assets/posts/en/home-assistant-notifications-on-telegram/image06.jpg)

## Conclusion

Thanks to these simple steps we have configured a *Telegram bot* and its integration with *Home Assistant* to receive notifications of everything we can think of. We can even create actions with buttons inside the *bot* itself to control various aspects of our smart home.

This and other articles complement the documentation of the [***GitHub* repository**](https://github.com/danimart1991/home-assistant-config) where all the configuration of my house is available.
