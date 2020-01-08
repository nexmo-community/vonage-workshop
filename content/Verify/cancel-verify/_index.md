---
title: "Cancel Verify Request"
weight : 50
---

Cancelling a Verify request is essentially the same as telling it to advance to the next stage, you will just send a control request with the command set to 'cancel'

Create a file called `cancel_verify_request.js` and add the following to it:

```js
const Nexmo = require("nexmo")
require('dotenv').config();

const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET
  })

nexmo.verify.control({
    request_id: process.env.REQUEST_ID,
    cmd: 'cancel'
  }, (err, result) => {
    if (err) {
      console.error(err);
    } else {
      console.log(result);
    }
  });
```

run this with `REQUEST_ID=VERIFY_REQUEST_ID node cancel_verify_request.js`

> NOTE: This must be run 30 seconds after the start of your verify request or it will be flagged as too early!