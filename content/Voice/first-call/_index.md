---
title: "Make Your First Call"
weight : 15
hidden: false
---


## Overview 

In this activity, you write code to call your mobile phone programmatically.

### What You'll Need

* A Vonage account if you don't have one already. Sign up here: <https://dashboard.nexmo.com/sign-up>
* A Vonage phone number to make calls with. You can [check your existing numbers](https://dashboard.nexmo.com/your-numbers) and [buy numbers](https://dashboard.nexmo.com/buy-numbers) using the Developer Dashboard.
* A Vonage Application - you need both the application ID and the private key file copied somewhere safe. From the [dashboard](https://dashboard.nexmo.com), visit "Voice" and then "Create an application". Give your new application a name and choose "Generate public and private key"; your browser will download the private key. Set your application to have Voice capabilities (you can use `example.com` URLs for now, we will update these later) and save it. Once you have created the application, use the Dashboard to link it to the Vonage number you will use.
* A number you can phone (probably your mobile phone).


Optional, but recommended:

* The [Nexmo CLI tool](https://developer.nexmo.com/tools) may be a nicer way to work with the number purchase, application creation, etc.


## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `outbound-call`.

```
mkdir outbound-call
cd outbound-call
npm init -y
```

Run the following command to install the required dependencies: `dotenv` for loading environment variables and the `nexmo` SDK for JavaScript/Node.js:

```
npm install nexmo dotenv
```

Copy the `private.key` file you downloaded from the Developer Dashboard into the root of `outbound-call`.

### Write the code

Create a new file called `outbound-call.js` and add the following code:

```js
const Nexmo = require('nexmo');
require('dotenv').config();
const nexmo = new Nexmo({
  apiKey: process.env.API_KEY,
  apiSecret: process.env.API_SECRET,
  applicationId: process.env.APPLICATION_ID,
  privateKey: process.env.PRIVATE_KEY_PATH
})

nexmo.calls.create({
  to: [{
    type: 'phone',
    number: process.env.TO_NUMBER
  }],
  from: {
    type: 'phone',
    number: process.env.VIRTUAL_NUMBER
  },
  ncco: [{
    "action": "talk",
    "text": "This is a text to speech call from Vonage"
  }]
})
```

### Configure your application

Create a file called `.env` in the same directory as `outbound-call.js` so that you can configure it with appropriate values for the constants (in capital letters) in your code.

- `API_KEY`: Your API key from the Dashboard
- `API_SECRET`: Your API secret from the Dashboard
- `APPLICATION_ID`: Your Voice API application ID
- `PRIVATE_KEY_PATH`: The path to your downloaded `private.key` file
- `VIRTUAL_NUMBER`: Your Vonage Virtual Number
- `TO_NUMBER`: Your mobile number

Both numbers should be in [E.164 format](/basic-concepts/number-format/).

For example:

```text
API_KEY=7s53a83b
API_SECRET=Mg3UXXPqxhSBH77s1
VIRTUAL_NUMBER=447700900000
TO_NUMBER=447700900001
APPLICATION_ID=674aed11-3e19-467f-a74e-e2de4f9693b8
PRIVATE_KEY_PATH=private.key
```

### Run it!

In your terminal run `node outbound-call.js`

### Outcome

If everything is working correctly, your mobile phone should receive a text-to-speech call from Vonage.


## Next Steps

### Customise your call

What would you like to hear? Check out the `talk` action in our [NCCO reference documentation](https://developer.nexmo.com/voice/voice-api/ncco-reference) to learn what else you can do, if you'd like your spoken greeting to have a bit more expression you can investigate [SSML (Speech Synthesis Markup Language)](https://developer.nexmo.com/voice/voice-api/guides/customizing-tts).

### Track events (insight and debugging tactic)

Go back to the dashboard and configure the application's `event_url` endpoint. You can either point this to:

* An event webhook within your application (probably using [ngrok](https://ngrok.com)), we'll be doing another incoming webhook in the next exercise anyway
* A tool such as the [Voice Event Logger](https://github.com/Nexmo/voice-event-logger)
* A general webhook receiver like Requestbin (still available at <http://bin.on.dockerize.io/> and <http://requestbin.net>) or [Postbin](https://postb.in/)

## Alternative Languages

See the [Make an Outbound Call with NCCO example](https://developer.nexmo.com/voice/voice-api/code-snippets/make-an-outbound-call-with-ncco) on VDP. Click through to view the full source on GitHub.

