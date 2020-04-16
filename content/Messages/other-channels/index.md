---
title: "WhatsApp and Viber"
weight : 12
hidden: false
---

The WhatsApp and Viber channels require some configuration before they are used. However, you can use our [sandbox](https://dashboard.nexmo.com/messages/sandbox) environment if you want to play around with them.

Visit that page and follow the instructions for the channels that you are interested in to get yourself set up. If you want to use those channels in an app, however, you need to use different API endpoints!

Usually, you make Message API `POST` requests to:

`https://api.nexmo.com/v0.1/messages/`

However, if you want to send yourself messages using our sandbox accounts then you will need to use the following endpoint:

`https://messages-sandbox.nexmo.com/v0.1/messages/`

In order to do that, you will need to configure the `nexmo` client in your Node.js app at the point of instantiation, by specifying the `apiHost`:

```javascript
const nexmo = new Nexmo({
  apiKey: process.env.API_KEY,
  apiSecret: process.env.API_SECRET,
  applicationId: process.env.APPLICATION_ID,
  privateKey: process.env.PRIVATE_KEY_FILE
}, {
  debug: true,
  // use Sandbox endpoint
  apiHost: 'messages-sandbox.nexmo.com'
});
```