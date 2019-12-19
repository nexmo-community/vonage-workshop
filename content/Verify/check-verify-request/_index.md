---
title: "Check Verify Request"
weight : 20
---

After creating a verify request in the previous section, the next step naturally is to check it.

In order to do this, you'll be using the request ID produced in the previous exercise combined with the PIN code forwarded to your device.

## Programming the Check request 

Create a new file called `check_verify.js`

In this file add the following code:

```js
const Nexmo = require("nexmo")
require('dotenv').config();

const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET
  })

nexmo.verify.check({
    request_id: process.env.REQUEST_ID,
    code: process.env.CODE
  }, (err, result) => {
    if (err) {
      console.error(err);
    } else {
      console.log(result);
    }
  });
```