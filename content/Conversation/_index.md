---
title: "Conversation"
weight : 8
---

## Conversation API Overview

The Conversation API enables developers and enterprises to create customized, real-time conversations that maintain context across multiple channels, including messaging and voice.

The Conversation API is a low-level API that allows you to create various objects such as Users, Members, and Conversations.

Conversations are the fundamental concept the API revolves around. Conversations are containers of communications exchanged between two or more Users which could be a single interaction or the history of all interactions between them.

The API also allows you to create Events and Legs to enable text, voice and video communications between two Users and store them in Conversations.

Text, voice and video communications can currently flow through various Channels like App, Phone, SIP, and Websocket. To enable the App channel (for in-app messaging, voice and video), you would need to also utilize our [Client SDK](https://developer.nexmo.com/client-sdk/overview).

Phone, SIP and Websocket Channels are enabled through the Voice API and they all flow into Conversations.

> **Note**: The Conversation API is currently in Beta.

## Resources

* [Conversation Docs](https://developer.nexmo.com/conversation/overview)
* [Event Flow Example](https://developer.nexmo.com/conversation/guides/event-flow)
* [Voice Events](https://developer.nexmo.com/voice/voice-api/webhook-reference)