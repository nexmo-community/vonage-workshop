---
title: "Certification Project"
weight : 999
hidden: false
---

## Messages and Dispatch APIs Certification

### Objective

You have learned how to send messages with the Messages API, but now you need to include the [Dispatch API](https://developer.nexmo.com/dispatch/overview) so that you can fail over from one channel to another!

Your job is to create an application that notifies your customer that their package will be delivered tomorrow.

Initially, you will attempt to contact them using a plain SMS message.

If that fails to elicit a response (and, for the purposes of this exercise let's assume that you'll wait 180 seconds), you should failover to either a second (but different) SMS message or a Facebook Messenger message containing an image.

You don't need to worry about inbound messages - let's just assume that your user is unresponsive.

### Delivery

Please send your completed project to `mark.lewin@vonage.com` - a link to a repo on Github is preferable. Please also provide a brief `README` to describe how to run your project.

> **Note**: There is no deadline for this, but please be aware that your coupon credit will expire on 30th April.

### Resources

The following resources will help you:

* [Dispatch API](https://developer.nexmo.com/dispatch/overview)
* [Send an Image Message using FB Messenger](https://developer.nexmo.com/messages/code-snippets/messenger/send-image)
* [Send an SMS](https://developer.nexmo.com/messages/code-snippets/sms/send-sms)



> **Good luck!**: Feel free to reach out to me at `mark.lewin@vonage.com` with any questions and I will do my best to help. Also consider helping each other out on the Slack channel - the best way to learn is to teach!
