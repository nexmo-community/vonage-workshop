---
title: "Create Verify Request"
weight : 10
---

This activity shows you how to initiate the verification process by sending your user a PIN. 

## What you'll need

1. A mobile phone to receive the PIN that is sent by the service
2. Your `NEXMO_API_KEY`
3. Your `NEXMO_API_SECRET`

## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `verify-request`.

```
mkdir verify-request
cd verify-request
npm init -y
```

Run the following command to install the required dependencies: `dotenv` for loading environment variables and the `nexmo` SDK for JavaScript/Node.js:

```
npm install nexmo dotenv
```

### Write the code

Create a new file called `request.js` and add the following code:

```js
const Nexmo = require("nexmo")
require('dotenv').config()

const nexmo = new Nexmo({
    apiKey: process.env.API_KEY,
    apiSecret: process.env.API_SECRET
  })

nexmo.verify.request({
    number: process.env.TO_NUMBER,
    brand: process.env.BRAND_NAME
}, (err,result)=>{
    if (err) {
        console.error(err);
      } else {
        const verifyRequestId = result.request_id
        console.log('request_id', verifyRequestId)
      }
})
```

### Configure your application

Create a file called `.env` in the same directory as `request.js` so that you can configure it with appropriate values for the constants (in capital letters) in your code.

```text
NEXMO_API_KEY=
NEXMO_API_SECRET=
TO_NUMBER=
BRAND_NAME=
```

- `TO_NUMBER` should be in [E.164 format](/basic-concepts/number-format/).
- `BRAND_NAME` identifies the sender.

For example:

```text
API_KEY=7s53a83b
API_SECRET=Mg3UXXPqxhSBH77s1
TO_NUMBER=447700900001
BRAND_NAME=My Super App
```

### Run it!

In your terminal run `node request.js`

### Outcome

If everything is working correctly, your terminal will display the unique `request_id` and your mobile phone will receive a message from `BRAND_NAME` containing a PIN.

```text
request_id 438d883c219a46098a83549aa45eed4a
```

![verification message on phone](/images/phone-verify-request.png)

Shortly thereafter, the verification service will attempt to deliver the code via a text-to-speech phone call. Listen to the message and then hang up. The service will attempt another phone call: again, listen to it and then hang up. This is the default workflow.

## A note on workflows

The Verify API supports two key types of verification: 

1. SMS messages
2. Text-to-speech (TTS) phone calls

You can manipulate which of these types you use, and in which order by changing the `workflow_id` parameter of your request:

You can choose from the following workflows: 

* `workflow_id: 1` - this is the default workflow and goes SMS->TTS->TTS
* `workflow_id: 2` - SMS->SMS->TTS
* `workflow_id: 3` - TTS->TTS
* `workflow_id: 4` - SMS->SMS
* `workflow_id: 5` - SMS->TTS
* `workflow_id: 6` - SMS
* `workflow_id: 7` - TTS

Feel free to experiment!

Each workflow has a prescribed set of timeouts between the actions in it. You can read more about the workflows [here](https://developer.nexmo.com/verify/guides/workflows-and-events).

This timeout between actions can be overridden by setting the `next_event_wait` parameter in the request.

## Next steps

Once you have the PIN, you need to provide the user with a way to submit it to your application so that you can then check that it was the same one that was sent. You will do this in [the next activity](/verify/check-verify-request).

## Alternative Languages

See the [send verification code example](https://developer.nexmo.com/verify/code-snippets/send-verify-request) on NDP. Click through to view the full source on GitHub.
