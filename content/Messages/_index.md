---
title: "Messages and Dispatch"
weight : 6
hidden: true
---

## Messages and Dispatch

The **Messages API** allows you to send and in some cases receive messages over the following communications channels:

* SMS/MMS
* Facebook Messenger
* Viber
* WhatsApp

More channels may be supported in the future.

The **Dispatch API** enables the developer to send messages to users using a multiple channel strategy.

For example, you could code your app to send a message via Facebook Messenger, and if that message is not read then the user can be sent a message via Viber. If that message is also not read, a user could then be sent a message via SMS.

The Dispatch API provides the mechanism by which to order messages and specify their success conditions. The Dispatch API uses the Messages API to actually send the messages.


> **Note**: The Messages and Dispatch APIs are currently in Beta.

## Resources

* [Interview with Hugh Hopkins, PM](https://www.vonage.com/resources/articles/simplifying-messaging-introducing-nexmo-messages-api-dispatch-api-beta/)