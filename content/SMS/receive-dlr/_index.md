---
title: "Get a Delivery Receipt"
weight : 30
---

When you make a successful request to the SMS API, it returns an array of message objects, one for each message. Ideally these will have a status of `0`, indicating success. But this does not mean that your message has reached your recipients. It only means that your message has been successfully queued for sending.

Nexmo's adaptive routing then identifies the best carrier for your message. When the selected carrier has delivered the message, it returns a delivery receipt (DLR).

In this activity, you learn how to request a delivery receipt so that you can have (some) confidence that your message has been delivered.

> Note: In most situations, a DLR is a reliable indicator that a message was delivered. However, it is not an absolute guarantee. [See How Delivery Receipts Work](https://developer.nexmo.com/messaging/sms/guides/delivery-receipts#how-delivery-receipts-work)


## What you'll need

1. Your working [send an sms](/sms/send-sms) example
2. A Nexmo virtual number to send an SMS from - see [here](/basic-concepts/numbers)
3. A mobile phone to send the SMS to
4. `ngrok` installed - see [here](/basic-concepts/ngrok)

## Steps

Getting a delivery receipt is similar to [receiving an inbound SMS](/sms/receive-sms), in that you need to create a webhook that Vonage's API platform can send the delivery receipt to.

So you need to create your webhook, make it accessible over the public Internet and configure your account to use it.


### Create a Node.js application

The following commands create a Node.js application in a directory called `dlr-sms` and install the required dependencies: the `express` web framework and `body-parser` for dealing with `POST` request bodies. 

```
mkdir dlr-sms
cd dlr-sms
npm init -y
npm install express body-parser
```

Create a file called `receive-dlr.js` in your new directory and populate it with the following code to create a webhook for delivery receipts:

```js
const app = require('express')()
const bodyParser = require('body-parser')

app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))

app
  .route('/webhooks/dlr')
  .get(handleDlr)
  .post(handleDlr)

function handleDlr(req, res) {
  console.log(req.body);
  res.status(204).send()
}

app.listen(process.env.PORT || 3000)
```

### Make your webhook accessible

Use `ngrok` to make your webhook accessible over the public Internet:

```
./ngrok http 3000
```

The URL that `ngrok` provides will form the domain part of your webhook. Append `/webhooks/dlr` to it, and this is the full URL of your webhook.

### Configure your account

In the [receive sms](/sms/receive-sms) example, you set the webhook URL on the number you wanted to receive SMS at. You also used the CLI to achieve this.

Setting the delivery receipt webhook is a little different, because you can't use the CLI to do it. You must either use the Dashboard or the Account API.

Also, you set the delivery receipt webhook for *all* your numbers, rather than for each. (You also have the option to do this for inbound SMS). 

Visit the [settings page](https://dashboard.nexmo.com/settings) in the Developer Dashboard and enter your delivery receipt webhook URL in the textbox highlighted in the diagram below:

![Set Delivery Receipt Webhook](/images/dashboard-set-dlr-webhook.png)

## Run it!

1. Ensure that `ngrok` is still running and that your delivery receipt webhook URL is up to date.

2. Start your server:

  ```
  node receive-dlr.js
  ```

3. [Send a test SMS](/sms/send-sms) from your Nexmo mobile number to your mobile phone.

## Outcome

If everything is working correctly, your mobile phone will receive an SMS and the delivery receipt with `status: delivered` and `err-code: 0` should display in the console:

```
{
  msisdn: '442079460000',
  to: '442079460001',
  'network-code': '23420',
  messageId: '14000000B1AA4386',
  price: '0.03330000',
  status: 'delivered',
  scts: '2002101103',
  'err-code': '0',
  'api-key': '7c47b66b',
  'message-timestamp': '2020-02-10 11:03:54'
}
```

## Alternative Languages

See the [Delivery receipt example](https://developer.nexmo.com/messaging/sms/code-snippets/delivery-receipts) in the docs. Click through to view the full source on Github.