---
title: "Create Verify Request"
weight : 10
---

Creating a verify request will simply consist of firing off a request, and awaiting the user to input a response.

* Run `npm install nexmo dotenv` to ensure all our dependcies are installed

* Let's create the Create Request code. Create a file called `create_verify_request.js` In here add the following code

```js
const Nexmo = require("nexmo")
require('dotenv').config();

const nexmo = new Nexmo({
    apiKey: process.env.NEXMO_API_KEY,
    apiSecret: process.env.NEXMO_API_SECRET
  })

nexmo.verify.request({
    number: process.env.TO_NUMBER,
    brand: process.env.NEXMO_BRAND
}, (err,result)=>{
    if (err) {
        console.error(err);
      } else {
        const verifyRequestId = result.request_id;
        console.log('request_id', verifyRequestId);
      }
})
```

Let's also create a `.env` file that will configure the app for us add the following fields - replacing with appropriate values:

```text
NEXMO_API_KEY=NEXMO_API_KEY
NEXMO_API_SECRET=NEXMO_API_SECRET
TO_NUMBER=TO_NUMBER
NEXMO_BRAND=Acme
```

With this setup we can now create a verify request with `node create_verify_request.js` - this will start the verify request. The next step is going to be checking the verify request.