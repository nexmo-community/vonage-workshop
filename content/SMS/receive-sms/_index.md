---
title: "Receive an SMS"
weight : 30
---

In this section we will be learning how to receive an SMS message back from the Nexmo API.

## Our Server Code

Let's start by creating a little server.js file in a new directory - make sure you run `npm express body-parser` in that directory to pull in our requirements

add this code to server.js

```js
const app = require('express')()
const bodyParser = require('body-parser')
 
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
 
app.listen(process.env.PORT || 5000);

app.post('/webhooks/inbound-message', (req, res) => {
    console.log(req.body);
    res.status(200).end();
  });
```

All this will to do is to print out the body of the inbound message request when it's received

## Run our code

let's run this by using `node server.js`

## Tunnel ngrok to our code

let's run `ngrok http 5000 -region=in` to create a tunnel to your server.

## set the webhooks for nexmo

In order for Nexmo to know where to send the traffic we will need to configure the webhooks for the number we want to message. There are several ways to do this - the most obvious being through the CLI or through the Dashboard:

CLI

to update the number from the CLI simply use the number update or nu command like so:

`nexmo nu --mo_http_url <webhook_url> <number_you_wish_to_update>`

Dashboard:

you can hop into your account go to numbers->my numbers->manage a number, then set the inbound webhook for the number see below:

![set inbound webhook gif](/gifs/set_webhook_per_number.gif)


## And That's It!

With all this set you're ready to fire off SMS' to your endpoint - try texting your server - you should get a response in your console!