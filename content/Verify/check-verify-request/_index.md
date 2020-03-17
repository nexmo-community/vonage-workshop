---
title: "Check Verify Request"
weight : 20
---

Now that you know how to [initiate the verify request](/verify/create-verify-request), in this activity you'll learn how to check it.

To do this, you'll be using the `request_id` you generate from the previous activity combined with the PIN code forwarded to your device.

## What you'll need

1. A mobile phone to receive the PIN that is sent by the service
2. Your `NEXMO_API_KEY`
3. Your `NEXMO_API_SECRET`

## Steps

### Create a Node.js application

The following commands create a Node.js application in a directory called `verify-check`.

```
mkdir verify-check
cd verify-check
npm init -y
```

Run the following command to install the required dependencies: `dotenv` for loading environment variables and the `nexmo` SDK for JavaScript/Node.js:

```
npm install nexmo dotenv
```

### Write the code

Create a new file called `check.js` and add the following code:


```js
const Nexmo = require("nexmo")
require('dotenv').config()

const nexmo = new Nexmo({
    apiKey: process.env.API_KEY,
    apiSecret: process.env.API_SECRET
  })

nexmo.verify.check({
    request_id: process.env.REQUEST_ID,
    code: process.env.CODE
  }, (err, result) => {
    if (err) {
      console.error(err)
    } else {
      console.log(result)
    }
  })
```


### Configure your application

Create a file called `.env` in the same directory as `check.js` so that you can configure it with appropriate values for the constants (in capital letters) in your code.

```text
API_KEY=
API_SECRET=
REQUEST_ID=
CODE=
```

Complete the `API_KEY` and `API_SECRET` fields using your API key and secret. Leave `REQUEST_ID` and `CODE` blank for now.

## Run it!

You'll need to generate a new code because the one you created in the previous activity will have timed out by now.

First, change your verify request code (in `request.js`) to use `workflow_id: 6`. This will send the code via a single SMS (to avoid too many beeps in class!):

```javascript
nexmo.verify.request({
    number: process.env.TO_NUMBER,
    brand: process.env.BRAND_NAME,
    worflow_id: 6
}, (err,result)=>{
    if (err) {
        console.error(err);
      } else {
        const verifyRequestId = result.request_id
        console.log('request_id', verifyRequestId)
      }
})
```

Run `request.js` to generate the `request_id` and PIN:

```
node request.js
```

Take the `request_id` that displays in the console and the PIN that you receive on your mobile device and use them to populate `REQUEST_ID` and `CODE` respectively in your `.env` file. Do this as quickly as possible to avoid timeouts.

Then, run `check.js`:

```
node check.js
```

## Outcome

The console displays the result of the check. If the `status` is `0`, the code you entered is the same as the one that was sent:

```text
{ request_id: 'a79c79186a7448c9b3007e072e05c0f4',
  status: '0',
  event_id: '14000000B21AB08C',
  price: '0.05000000',
  currency: 'EUR',
  estimated_price_messages_sent: '0.03330000' }
```

## Alternative Languages

See the [check verification code example](https://developer.nexmo.com/verify/code-snippets/check-verify-request) on NDP. Click through to view the full source on GitHub.