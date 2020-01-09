---
title: "Receiving Delivery Receipts"
weight : 40
---

## Isn't my Successful SMS Request Enough?

When you make a successful request to the SMS API, it returns an array of message objects, one for each message. Ideally these will have a status of 0, indicating success. But this does not mean that your message has reached your recipients. It only means that your message has been successfully queued for sending.

## Oh, So What if I Want to Know My Message Has Been Delivered?

Nexmo's adaptive routing then identifies the best carrier for your message. When the selected carrier has delivered the message, it returns a delivery receipt (DLR).

> Note: In most situations, a DLR is a reliable indicator that a message was delivered. However, it is not an absolute guarantee. [See How Delivery Receipts work](https://developer.nexmo.com/messaging/sms/guides/delivery-receipts#how-delivery-receipts-work)

## How Do I Receive A DLR

Receiving a Delivery Receipt from a sent SMS messages works in a similar fashion to receiving an inbound SMS message in that it comes back via webhook. However it must be set from either the dashboard or the Account API as the CLI doesn't contain a means of setting it.

Create a file: `receive_dlr.js`

Add the following code to it:

```js
const app = require('express')()
const bodyParser = require('body-parser')
const Nexmo = require('nexmo')

app
  .route('/webhooks/dlr')
  .get(handleDlr)
  .post(handleDlr)

  function handleDlr(request, response){
      console.log(request);
      response.status(204).send()
  }
  app.listen(process.env.PORT || 3000)
```

* Set your accounts webhooks dlr to point at your route.
* Start the dlr server with `node receive_dlr.js`
* Send an SMS over the Nexmo API.