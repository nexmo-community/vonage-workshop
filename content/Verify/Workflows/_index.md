---
title: "Workflows"
weight : 30
---

## Overview

The Verify API supports two key types of verification 

1. SMS Messages
2. Text To Speech (TTS) Phone calls

You can manipulate which of these types you use, and in which order by changing the workflow_id parameter of your request.

There are currently 7 key types of workflows 

* Workflow 1 id: 1 - this is the Default workflow and goes SMS->TTS->TTS
* Workflow 2 id: 2 - SMS->SMS->TTS
* Workflow 3 id: 3 - TTS->TTS
* Workflow 4 id: 4 - SMS->SMS
* Workflow 5 id: 5 - SMS->TTS
* Workflow 6 id: 6 - SMS
* Workflow 7 id: 7 - TTS

Each workflow has a prescribed set of timeouts between the actions in it - you can read more about it on the [Nexmo Developer Portal](https://developer.nexmo.com/verify/guides/workflows-and-events)

This timeout between actions can be overridden by setting the `next_event_wait` parameter in the request

## Sending a Verify request with an Altered workflow

This really is just a matter of sending a verify request with an altered workflow id. Create a new file `start_verify_with_workflow.js`

Add the following code to the file:

```js
const Nexmo = require("nexmo")
require('dotenv').config();
console.log("API KEY: " + process.env.NEXMO_API_KEY);

const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET
  })

nexmo.verify.request({
    number: process.env.TO_NUMBER,
    brand: process.env.NEXMO_BRAND,
    workflow_id: process.env.WORKFLOW_ID
  }, (err, result) => {
    if (err) {
      console.error(err);
    } else {
      const verifyRequestId = result.request_id;
      console.log('request_id', verifyRequestId);
    }
  });
  ```

  And you can run it using our old .env file and `WORKFLOW_ID=7 node start_verify_with_workflow.js` This will kick off a Text-To-Speech only verify interaction.