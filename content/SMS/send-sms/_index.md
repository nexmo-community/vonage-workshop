---
title: "Send an SMS"
weight : 10
---

Sending an SMS is really a fairly simple operation and in here we'll explroe how to do it.

## what you'll need

1. A nexmo Number,
2. Your Phone number
3. Your Nexmo Number
4. Your NEXMO_API_KEY
5. Your NEXMO_API_SECRET

### installing dependencies

Open your terminal and run `npm install nexmo` to install the Nexmo js sdk.

### Writing the Code

create a new file `send_sms.js` and add the following vold - replace anything in screaming caps with an appropriate value

```js
const Nexmo = require('nexmo')

const nexmo = new Nexmo({
  apiKey: "NEXMO_API_KEY",
  apiSecret: "NEXMO_API_SECRET"
})
	
let text = "👋Hello from Nexmo";
 
nexmo.message.sendSms("NEXMO_NUMBER", "TO_NUMBER", text, {
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

### Running the Code

In your terminal run `node send_sms.js`

### And That's It!

It really is as simple as that - up next is receiving an SMS!