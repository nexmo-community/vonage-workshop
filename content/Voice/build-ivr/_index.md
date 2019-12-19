---
title: "Building an IVR"
weight : 45
---

An IVR or Interactive Voice Response is a menu of options presented to a caller, they then navigate that menu using the buttons on their keypad to send DTMF (Dual Tone Multi Frequency) signals. Each option on the IVR can direct the call to a different path, for example forwarding the call to a specific destination, playing a recorded piece of information or even triggering another service such as an SMS. IVRs are the fundamental navigation method of Voice Call applications. An IVR can have multiple levels to it, where selection of one option presents the user with more options, this can go on to an infinite depth! For this activity we will just create a single level IVR.

## Inital Call Handling

Start by reusing the logic built in the previous section

Prepare your dependencies: `npm install nexmo express body-parser`

Now the code we are going to use here is going to vary slightly from the previous section's

Let's start by creating an index.js file and just get the basics out of the way.

require app and body parser, setup our routes, and set the app up to listen on port 3000

```js
const app = require('express')()
const bodyParser = require('body-parser')
const origin_phone_number = "NEXMO_NUMBER";
const sales_office_number = '15558675309';
const base_url = "https://www.example.com";
app.use(bodyParser.json())
//When adding code, add after this line

//When adding code, add before this line
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

Now we have constructed a talk action to explain the menu to the customer, and we have provided an input action for customers to send input in through.

The next step is to actually manage all that user input. In the previous section we simply echoed the user's input back to them. Here we are just going to switch on the different possible inputs.

* For the sales department we will connect them immediately with a representative at the sales_office_number.
* For the Support department, we will collect a 5 digit account number from them.
* For the press office, unfortunatley no one can take the call, and we haven't set up our voice mail system - so we will have to drop it.

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

Finally if they contacted the support department we will need to echo the account input back to them and tell them they'll be contacted later. Add the following into our index.js file

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

With this last piece of the puzzle we're ready to go.

To fire this up do the following:

* run `http ngrok 3000` to point your ngrok tunnel at your server

![ngrok](/images/ngrok.png)

* take the forwarded address and set that as your base_url in the case of the above example this will be http://c1b4d1a1.ngrok.io
* replace the sales_office_number with a phone number you want it to call
* replace the origin_phone_number with your nexmo number
* run `node index.js`

And that's it! - now all you need to is call into your voice app with your phone and you'll be dialed into the IVR