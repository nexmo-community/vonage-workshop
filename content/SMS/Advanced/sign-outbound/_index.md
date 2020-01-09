---
title: "Sign Outbound SMS"
weight : 10
---

Sending a signed SMS is very similar programtically to sending a regular SMS - the only thing different you will need is the Signature Secret and the Signature Method for your account. This can be found in your Dashboard under account settings

### Writing Your Code

Equipped with these items you can now build the code - create a new file `send_signed_sms.js` and add the following - replace the bolded sections with the appropriate items.

```js
const Nexmo = require('nexmo')
require('dotenv').config();
const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET,
    signatureSecret: process.env.NEXMO_API_SIGNATURE_SECRET,
    signatureMethod: "md5hash"
  })

const from = process.env.FROM_NUMBER
const to = process.env.TO_NUMBER
const text = 'A text message sent using the Nexmo SMS API'

nexmo.message.sendSms(from, to, text, (err, responseData) => {
    if (err) {
        console.log(err);
    } else {
        if(responseData.messages[0]['status'] === "0") {
            console.log("Message sent successfully.");
        } else {
            console.log(`Message failed with error: ${responseData.messages[0]['error-text']}`);
        }
    }
})
```

### Running the code

This can be run with `node send_signed_sms.js`