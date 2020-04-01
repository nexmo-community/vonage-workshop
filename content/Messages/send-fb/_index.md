---
title: "Send/Receive Facebook Messages*"
weight : 2
hidden: false
---

In this exercise, you will send messages by using Facebook Messenger. The process is very similar to sending an SMS/MMS, except there is some setup involved to create a Facebook page and link it to your virtual number.

**Obviously, this step requires a Facebook account and not everyone has or wants one! So this is an optional activity.**

If you want to proceed, read and follow the steps outlined in [this tutorial](https://developer.nexmo.com/use-cases/sending-facebook-messenger-messages-with-messages-api).

If you want to be able to respond to the Facebook messages that you send, you will need a webhook server, as outlined in [this section of the tutorial](https://developer.nexmo.com/use-cases/sending-facebook-messenger-messages-with-messages-api#receive-a-facebook-message).

Here is some code you can use for that:

```javascript
const express = require('express');
const bodyParser = require('body-parser');
const app = express();

app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))

app.post('/webhooks/inbound-message', (req, res) => {
  console.log(req.body);
  res.status(200).end();
});

app.post('/webhooks/message-status', (req, res) => {
  console.log(req.body);
  res.status(200).end();
});

app.listen(3000)
```


Remember, that you'll need to execute it separately from the app that is sending the message and you'll need to expose your local URL to the Vonage API platform [using ngrok](/basic-concepts/ngrok).

You will also need to [set your webhooks](https://developer.nexmo.com/messages/code-snippets/configure-webhooks).

## Resources

* [Understanding Facebook messaging](https://developer.nexmo.com/messages/concepts/facebook)
* [Inbound message webhook](https://developer.nexmo.com/messages/code-snippets/inbound-message)
* [Message status webhook](https://developer.nexmo.com/messages/code-snippets/message-status)