---
title: "Send an SMS"
weight : 10
---

## Overview

In this activity, you send a text message using the SMS API.

### What you'll need

1. A Nexmo Number
2. A mobile phone to receive the SMS
3. Your `NEXMO_API_KEY`
4. Your `NEXMO_API_SECRET`

## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `send-sms`.

```
mkdir send-sms
cd send-sms
npm init -y
```

Run the following command to install the required dependencies: `dotenv` for loading environment variables and the `nexmo` SDK for JavaScript/Node.js:

```
npm install nexmo dotenv
```


### Write the code

Create a new file called `send-sms.js` and add the following code:

```js
const Nexmo = require('nexmo')
require('dotenv').config();
const nexmo = new Nexmo({
  apiKey: process.env.API_KEY,
  apiSecret: process.env.API_SECRET
})
	
let text = "👋 Hello from Vonage!";
 
nexmo.message.sendSms(process.env.FROM_NUMBER, process.env.TO_NUMBER, text, {
  type: "unicode"
}, (err, responseData) => {
  if (err) {
    console.log(err);
  } else {
    if (responseData.messages[0]['status'] === "0") {
      console.log("Message sent successfully.");
    } else {
      console.log(`Message failed with error: ${responseData.messages[0]['error-text']}`);
    }
  }
})
```

### Configure your application

Create a file called `.env` in the same directory as `send-sms.js` so that you can configure it with appropriate values for the constants (in capital letters) in your code.

- `API_KEY`: Your API key from the Dashboard
- `API_SECRET`: Your API secret
- `FROM_NUMBER`: Your Nexmo Virtual Number
- `TO_NUMBER`: Your mobile number

Both numbers should be in [E.164 format](/basic-concepts/number-format/).

For example:

```text
API_KEY=7s53a83b
API_SECRET=Mg3UXXPqxhSBH77s1
FROM_NUMBER=447700900000
TO_NUMBER=447700900001
```

### Run it!

In your terminal run `node send_sms.js`

### Outcome

If everything is working correctly, your terminal will display `Message sent successfully` and your mobile phone will receive a message from Vonage.

## Alternative Languages

See the [Send SMS example](https://developer.nexmo.com/messaging/sms/code-snippets/send-an-sms) in the docs. Click through to view the full source on GitHub.