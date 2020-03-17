---
title: "Advance to Next Event"
weight : 40
hidden: true
---

There are times when you send a verify request off with a given workflow where you will want to advance to the next stage of the verify request before the request would naturally move onto the next step.

To do this just send a trigger next event command into the verify API.

## Code it up

Create `verify_trigger_next_event.js` and add the following code to it:

```js
const Nexmo = require("nexmo")
require('dotenv').config()

const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET
  })

  nexmo.verify.control({
    request_id: process.env.REQUEST_ID,
    cmd: 'trigger_next_event'
  }, (err, result) => {
    if (err) {
      console.error(err)
    } else {
      console.log(result)
    }
  });
```

from there just run `REQUEST_ID=VERIFY_REQUEST_ID node verify_trigger_next_event.js` and it will force the verify request onto the next step