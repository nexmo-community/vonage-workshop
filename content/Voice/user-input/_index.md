---
title: "Interact with your user*"
weight : 35
hidden: true
---

## Overview

Making a call, or receiving one, is great but what next? In this exercise we'll interact with our user. To keep things simple, we'll prompt them to input digits on their keypad, but the same approaches would work with spoken interactions on both sides.

Over subsequent exercises, we will build on this to create an IVR (Interactive Voice Response) menu.

The outline of the process looks something like this:

1. Create an NCCO to prompt the user with a [`talk`](https://developer.nexmo.com/voice/voice-api/ncco-reference#talk) action to enter some digits which you will collect with an [`input`](https://developer.nexmo.com/voice/voice-api/ncco-reference#input) action. The `input` action accepts a URL so use your ngrok URL with a suffix - our examples use `/webhooks/dtmf`.
2. When the user sends some input, Vonage will alert your application using [an input webhook](https://developer.nexmo.com/voice/voice-api/webhook-reference#input).
3. Using the data that arrives in the webhook, we can return a new NCCO with custom content.

## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `get-input`.

```
mkdir get-input
cd get-input
npm init -y
```

Run the following command to install the required dependencies: the web development framework `express` for defining a route for your webhook and `body-parser` to handle `POST` requests:

```
npm install express body-parser
```

### Write the code

Create a file called `get-input.js` and add the following code to define the following routes:

- `/webhooks/answer`: to receive the inbound call
- `/webhooks/dtmf`: to collect the digits pressed by the user

```js
const app = require('express')()
const bodyParser = require('body-parser')

app.use(bodyParser.json())

const onInboundCall = (request, response) => {
  const ncco = [
    {
      action: 'talk',
      text: 'Please enter a digit'
    },
    {
      action: 'input',
      eventUrl: [`${request.protocol}://${request.get('host')}/webhooks/dtmf`]
    }
  ]

  response.json(ncco)
}

const onInput = (request, response) => {
  const dtmf = request.body.dtmf

  const ncco = [{
    action: 'talk',
    text: `You pressed ${dtmf}`
  }]

  response.json(ncco)
}

app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)

app.listen(3000)
```

## Run It!

Ensure that ngrok is running. If you left it running from the previous exercise, you won't need to update your Answer URL in the Developer Dashboard.

Run your code using `node get-input.js`.

Dial your Vonage Virtual number from your mobile phone.

## Outcome

If everything is working correctly, you should hear the greeting and then be prompted to enter a digit using your phone's keypad. This digit will then be read back to you.

## Alternative Languages

See the [Handle User Input with DTMF example](https://developer.nexmo.com/voice/voice-api/code-snippets/handle-user-input-with-dtmf) on VDP. Click through to view the full source on GitHub.

