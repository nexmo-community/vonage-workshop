---
title: "Receive an SMS"
weight : 20
---

In this activity, you receive a text message sent to your virtual number, using the SMS API.

## What you'll need

1. A Nexmo Number - you should alreaady have this, if you have completed the "send an SMS" activity
2. A mobile phone to send the SMS
3. The Nexmo CLI - see [here](/basic-concepts/nexmo-cli)
3. `ngrok` installed - see [here](/basic-concepts/ngrok)

## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `receive-sms` and install the required dependencies: the `express` web framework and `body-parser` for dealing with `POST` request bodies. 

```
mkdir receive-sms
cd receive-sms
npm init -y
npm install express body-parser
```

Create a file called `receive-sms.js` in your new directory and populate it with the following code to create a webhook for inbound SMS:


```js
const app = require('express')()
const bodyParser = require('body-parser')
 
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
 
app.listen(process.env.PORT || 3000);

app.post('/webhooks/inbound-sms', (req, res) => {
    console.log(req.body);
    res.status(200).end();
  });
```

## Configure your virtual number

So that Vonage knows to make a request to your webhook when you receive an SMS, you need to link your virtual number to it. First, though, you need to ensure that your webhook is visible to Vonage's APIs.


### Run ngrok

For the webhook that you're going to develop as part of this activity to be available to Vonage's APIs, it needs to be accessible over the public Internet. You can achieve this by creating a secure tunnel using `ngrok`, which you installed earlier.

Run `ngrok` on port 3000 or any other port of your choice, using the following command:

```
./ngrok http 3000
```

The URL that `ngrok` provides will form the domain part of your webhook. Append `/webhooks/inbound-sms` to it, and this is the full URL of your webhook.


### Associate your number with your webhook

You need to associate your virtual number with your webhook URL so that Vonage knows to make a request to your webhook when you receive an SMS at that number. 

For example: `https://a992f76d.ngrok.io/webhooks/inbound-sms`.

Execute the following CLI command to perform the link, replacing `YOUR_VIRTUAL_NUMBER` and `YOUR_WEBHOOK_URL`.

> **Note**: Ensure that your number is in [E.164 format](/basic-concepts/number-format/). 

```
nexmo link:sms YOUR_VIRTUAL_NUMBER YOUR_WEBHOOK_URL
```

> **Note**: If you restart `ngrok` for any reason, the URLs will change and you will have to repeat this step. 


### Using the Developer Dashboard

If you want to do this in the Developer Dashboard, just navigate to Numbers > My Numbers > Manage a Number and then set the Inbound Webhook URL for SMS:

![set inbound webhook gif](/gifs/dashboard-set-sms-webhook.gif)


## Run it!

1. Ensure that `ngrok` is still running and that your webhook URLs are up to date.

2. Start your server:

  ```
  node receive-sms.js
  ```

3. Send a test SMS to your virtual number.


## Outcome

If everything is working correctly, the details of the incoming message will appear in the console:

```
{ msisdn: '442079460000',
  to: '442079460001',
  messageId: '170000026809868F',
  text: 'This is a test message',
  type: 'text',
  keyword: 'THIS',
  'api-key': '7c47b66b',
  'message-timestamp': '2020-02-06 10:55:16' }
```


## Alternative Languages

See the [Receive SMS example](https://developer.nexmo.com/messaging/sms/code-snippets/receive-an-sms) in the docs. Click through to view the full source on GitHub.