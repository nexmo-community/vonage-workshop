---
title: "Build an IVR"
weight : 45
hidden: false
---

## Overview

An IVR or Interactive Voice Response is a menu of options presented to a caller, they then navigate that menu using the buttons on their keypad to send DTMF (Dual Tone Multi Frequency) signals. Each option on the IVR can direct the call to a different path, for example forwarding the call to a specific destination, playing a recorded piece of information or even triggering another service such as an SMS. IVRs are the fundamental navigation method of Voice Call applications. An IVR can have multiple levels to it, where selection of one option presents the user with more options, this can go on to an infinite depth! For this activity we will just create a single level IVR.

## Steps

### Inital Call Handling

Use the same project folder as you used in the previous [Interact with your user example](/voice/user-input/).

Create a new file called `ivr.js`

The code we are going to use will vary slightly from the previous example.



```js
const app = require('express')()
const bodyParser = require('body-parser')
const origin_phone_number = "VIRTUAL_NUMBER";
const sales_office_number = 'PHONE_NUMBER';
const base_url = "https://www.example.com";
app.use(bodyParser.json())

//Add extra code that you create in this exercise here

const onEvent =(request, response) =>{
    response.status(200).send();

}
app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)
  .post('/webhooks/events', onEvent)
  .post('/webhooks/accountInput', onAccountInput)

app.listen(3000)
```

Next we are going to want to give the user a set of options that they can choose from. Let's just have 3 options.

1. Sales
2. Customer Support
3. Press Office

Add the following` code to `ivr.js` in the position indicated by the comment:

```js
const onInboundCall = (request, response) => {
  const ncco = [
      {
        action: 'talk',
        text: 'Hello, welcome to Acme Systems Incorporated\'s Interactive Voice Response System. To speak with Sales press 1. For Customer Support press 2. For the press office, press 3',
        bargeIn: true
      },
      {
        action: 'input',
        eventUrl: [`${base_url}/webhooks/dtmf`],
        maxDigits: 1
      }
    ]
  
    response.json(ncco)
}
```

### Process user input

Now we have constructed a `talk` action to explain the menu to the customer, and we have provided an `input` action for customers to enter digits from their keypad.

The next step is to actually manage all that user input. In the previous section we simply echoed the user's input back to them. Here we are going to direct their call based on the option they selected.

* For the sales department we will connect them immediately with a representative using the number defined in the `sales_office_number` variable.
* For the Support department, we will collect a 5 digit account number from them.
* For the press office, unfortunately no one can take the call, and we haven't set up our voice mail system yet (we'll do that in the next exercise!) - so we will just have to hang up the call.

Put the following code underneath the definition of `onInboundCall()`:

```js
const onInput = (request, response) => {
    const dtmf = request.body.dtmf
    var ncco;

    switch(dtmf){
        case "1":
            ncco = [
                {
                action: 'talk',
                text: `You have asked to speak with the Sales Department, Connecting you now.`
                },
                {
                    action: 'connect',
                    from: origin_phone_number,
                    endpoint: 
                    [
                        {
                          "type": "phone",
                          "number": sales_office_number
                        }
                    ]

                }
            ]
            response.json(ncco)
            break;
        case "2":
            ncco = 
            [
                {
                    action: 'talk',
                    text: 'You have asked to speak with customer service, please input your 5 digit account number followed by the pound sign'
                },
                {
                    action: 'input',
                    eventUrl: [`${base_url}/webhooks/accountInput`],
                    timeOut: 10,
                    maxDigits: 6,
                    submitOnHash: true
                }
            ]
            response.json(ncco)
            break;
        case "3":
            ncco =
            [
                {
                    action: 'talk',
                    text: 'You have asked to speak with the press office. Unfortunately no one from the press office is currently available and the recording service has yet to be implemented, please try back later'
                }
            ]
            response.json(ncco)
            break;
        default:
            ncco = [
                {
                    action: 'talk',
                    text: 'I\'m sorry I didn\'t understand what you entered please try again'
                }
            ];
            response.json(ncco);
            break;
    }
}
```

If they contacted the support department we will need to echo the account input back to them and tell them they'll be contacted later. Add the following below the definition of `onInput()`:

```js
const onAccountInput =(request, response) =>{
    const dtmf = request.body.dtmf
    const input = dtmf.split('').join(' ');
    const ncco = 
    [
        {
            action: 'talk',
            text: 'Your account number is: ' + input + ' your case has been added and is being actively triaged, you will be contacted with an update to your case in 24 hours'
        }
    ];
    response.json(ncco);
    response.status(200).send();
  }
```

Now we're ready to test our application!

## Run It!

To fire this up do the following:

* run `http ngrok 3000` to point your ngrok tunnel at your server

![ngrok](/images/ngrok.png)

* Take the URL that `ngrok` gives you and set that as your `base_url`. In the case of the above example this will be http://c1b4d1a1.ngrok.io
* Replace the `sales_office_number` with a phone number that you own (but obviously, not the one you plan to call your application from!)
* Replace the `origin_phone_number` with your Vonage virtual number

Then, run `node ivr.js` and dial your Vonage virtual number.

## Outcome

If everything is working correctly, you should be presented with the IVR options and be able to choose one have your call dealt with accordingly.