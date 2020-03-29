---
title: "Receive an Inbound Call"
weight : 25
hidden: false
---

## Overview

To receive incoming calls, you need a publicly-available URL that will respond with a NCCO telling Vonage what to do with the call. One thing that's different from the previous example is that it does not need an API key - the Nexmo server will call your application this time rather than the other way around!


## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `inbound-call`.

```
mkdir inbound-call
cd inbound-call
npm init -y
```

Run the following command to install the required dependencies: the web development framework `express` for defining a route for your webhook and `body-parser` to handle `POST` requests:

```
npm install express body-parser
```

### Write the code

Create a file called `inbound-call.js` and add the following code to define a route called `/webhooks/answer`:

  ```js
  const app = require('express')()

  const onInboundCall = (request, response) => {
  const from = request.query.from
  const fromSplitIntoCharacters = from.split('').join(' ')

  const ncco = [{
      action: 'talk',
      text: `Thank you for calling from ${fromSplitIntoCharacters}`
  }]

  response.json(ncco)
  }

  app.get('/webhooks/answer', onInboundCall)

  app.listen(3000)
  ```

Test this new route by requesting <http://localhost:3000/webhook/answer> in your browser. You should see some JSON returned.

## Make your webhook endpoint visible

Your local URL needs to be publicly available so that the Vonage API platform can call it. One way to do this is to use [ngrok](https://ngrok.com):

Download and install `ngrok` using [our guide](/basic-concepts/ngrok).

Run ngrok by executing the following command in your terminal:

`ngrok http 3000`

When ngrok starts the tunnel, it will show you your URL, something like `https://abc123.ngrok.io` - copy the URL from the ngrok console as we will need it shortly.

In the [Developer Dashboard](https://dashboard.vonage.com), edit the Answer URL for your application, by pasting the ngrok URL and adding `/webhooks/answer` to the end of it, to make something like `https://abc123.ngrok.com/webhooks/answer`.

Keep ngrok running, otherwise the URL will change and you will have to update the Answer URL in the Dashboard.

## Run It!

Run your Node application:

`node inbound-call.js`

Call your application by dialing your Vonage virtual number from your mobile phone.

## Outcome
You should hear the spoken greeting which reads out the number you are calling from.

## Next Steps

### A more interesting greeting

We've shared our standard "answer a call" example but it isn't particularly interesting! What do you wish your application would do? How about:

* A talking clock
* A randomly chosen uplifting message
* Some music

Use the [NCCO reference documentation](https://developer.nexmo.com/voice/voice-api/ncco-reference) to try some alternative content for your Answer URL.

For more ideas, there's a set of [NCCO Examples on GitHub](https://github.com/nexmo-community/ncco-examples) that might help you along the way.

## Alternative Languages

See the [Receive an Inbound Call example](https://developer.nexmo.com/voice/voice-api/code-snippets/receive-an-inbound-call) on VDP. Click through to view the full source on GitHub.


