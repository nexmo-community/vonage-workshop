---
title: Send SMS and MMS
weight : 1
hidden : false
---

In this tutorial you will send an SMS using the Messages API and then adapt your solution to send an MMS

The same steps taken here can be easily modified to send a message across other channels, including Viber, Facebook Messenger and WhatsApp.


## The steps

### Create your project

Create a node.js project for this by running `npm init` and accepting all the defaults:

```bash
mkdir messages-sms
cd messages-sms
npm init
touch index.js
```

### Install dependencies

You need the beta version of the Nexmo Node SDK for this, and also `dotenv` for configuration:

```bash
npm install nexmo@beta dotenv
```

> **NOTE:** The benefit of using the SDK is that it handles all the JWT generation for you. If you are testing using `curl` or another language you will need to generate the JWT yourself. See [Creating a JWT](https://developer.nexmo.com/concepts/guides/authentication#using-the-nexmo-cli-to-generate-jwts).

### Write the code

Open the `index.js` file in your editor, and add the following code:

```javascript
require('dotenv').config()
const Nexmo = require('nexmo')

const nexmo = new Nexmo({
  apiKey: process.env.API_KEY,
  apiSecret: process.env.API_SECRET,
  applicationId: process.env.APPLICATION_ID,
  privateKey: process.env.PRIVATE_KEY
})

nexmo.channel.send(
  { "type": "sms", "number": process.env.TO_NUMBER },
  { "type": "sms", "number": process.env.FROM_NUMBER },
  {
    "content": {
      "type": "text",
      "text": "This is an SMS sent from the Messages API"
    }
  },
  (err, data) => { console.log(data.message_uuid); }
)
```

This code simply instantiates the `nexmo` client and sends an SMS with the Messages API. The SDK takes care of the generation of the JWT for you, so that you don't have to worry about it.

### Create a Nexmo Application

We're going to use the CLI for this, but you could also use the Developer Dashboard. You'll need the **beta** version of the CLI to create applications with the `messages` capability. Install it using `npm`:

```bash
npm install nexmo-cli@beta -g
```

Then create the application using the following command:

``` shell
nexmo app:create "My Messages App" --capabilities=messages --messages-inbound-url=https://example.com/webhooks/inbound-message --messages-status-url=https://example.com/webhooks/message-status --keyfile=private.key
```

This creates a Vonage application with the messages capability, with the webhook URLs configured as specified (just leave them as `example.com` for now), and generates a private key file `private.key` which it will download to your project directory, along with a `.nexmo-app` file that contains your application ID and private key details.


### Configure your app

Create a file called `.env` in your project directory and configure it using your own information:

```
API_KEY=<your_API_key>
API_SECRET=<your_API_secret>
APPLICATION_ID=<your_application_id>
PRIVATE_KEY=private.key
TO_NUMBER=<your_mobile_number>
FROM_NUMBER=<your_virtual_number>
```

## Run it!

Run your app:

```
node index.js
```

## Outcome

This should send an SMS message to the destination number you specified in `.env` and display the `message_uuid`, for example:

```
eabe7131-66f0-4b41-a428-1f42bca82561
```

## Next step

Now that you know how to send a plain old SMS using the Messages API, adapt your code to send an MMS.

Use [this code snippet](https://developer.nexmo.com/messages/code-snippets/mms/send-mms) to help you.

## Further reading

* [Messages documentation](https://developer.nexmo.com/concepts/overview)