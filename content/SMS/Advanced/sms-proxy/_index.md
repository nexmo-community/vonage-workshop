---
title: "SMS Proxy"
weight : 30
---

## Privatizing SMS communication

Proxying SMS messages provides us a means to allow two parties to SMS each other without needing to exchange contact information.

## Setup the server

Create a new folder `proxied_sms` and cd into that folder

run `npm install nexmo express body-parsers`

Create a file called `SmsProxy.js` and another called `server.js`


### Create our SmsProxy class


In SmsProxy.js require Nexmo and create a class called SmsProxy. Add a constructor which will build a Nexmo client from the process's environment. And Export the class.

```js
class SmsProxy {

    constructor() {

        this.nexmo = new Nexmo({
            apiKey: process.env.NEXMO_API_KEY,
            apiSecret: process.env.NEXMO_API_SECRET
        }, {
                debug: true
            });
    }
}
module.exports=SmsProxy;
```

### Create a Chat

In the SmsProxy class the next thing to do is to create a chat - this will assign the numbers for UserA and UserB. When this is done we'll send a message to each recipient to let them know whom they should message. Add the following code to your SmsProxy.js file.

```js
createChat(userANumber, userBNumber) {
        this.chat = {
            userA: userANumber,
            userB: userBNumber
        };
        console.log(userANumber);
    
        this.sendSMS();
    }
    sendSMS() {
        /*  
            Send a message from userA to the virtual number
        */
        console.log(this.chat.userA);
        this.nexmo.message.sendSms(process.env.VIRTUAL_NUMBER, this.chat.userA,
                                    'Reply to this SMS to talk to UserA');
    
        /*  
            Send a message from userB to the virtual number
        */
        console.log(this.chat.userA);
        this.nexmo.message.sendSms(process.env.VIRTUAL_NUMBER,this.chat.userB,
                                    'Reply to this SMS to talk to UserB');
    }
```

### Map the numbers

Next, map the numbers based off of the inbound numbers:

```js

getDestinationRealNumber(from) {
        let destinationRealNumber = null;
    
        // Use `from` numbers to work out who is sending to whom
        const fromUserA = (from === this.chat.userA);
        const fromUserB = (from === this.chat.userB);
    
        if (fromUserA || fromUserB) {
            destinationRealNumber = fromUserA ? this.chat.userB : this.chat.userA;
        }
    
        return destinationRealNumber;
    }
```

### Proxy the SMS

Now when you receive an inbound SMS you will determine the correct number to send to - and proxy the sms appropriately

```js
proxySms(from, text) {
        // Determine which real number to send the SMS to
        const destinationRealNumber = this.getDestinationRealNumber(from);
    
        if (destinationRealNumber  === null) {
            console.log('No chat found for this number');
            return;
        }
    
        // Send the SMS from the virtual number to the real number
        this.nexmo.message.sendSms(process.env.VIRTUAL_NUMBER,
                                    destinationRealNumber,
                                    text);
    }
```

### Build the server

Now in Server.js we'll create the server, and add some routes we'll need to proxy SMSs. With the 'inbound-sms' route we'll simply call the proxySms function, and with the 'chat' route we will actually create the chat - when it's all said and done it will look like:

```js
require('dotenv').config();
const express = require('express');
const bodyParser = require('body-parser');
const SmsProxy = require('./SmsProxy.js');

const app = express();
app.set('port', (process.env.PORT || 3000));
app.use(bodyParser.urlencoded({ extended: false }));

app.listen(app.get('port'), function () {
    console.log('SMS Proxy App listening on port', app.get('port'));
});

const smsProxy = new SmsProxy();

app.get('/', (req, res) => {
    res.send('Hello world');
})

app.post('/chat', (req, res) => {
    const userANumber = req.query['userANumber'];
    const userBNumber = req.query['userBNumber'];

    smsProxy.createChat(userANumber, userBNumber, (err, result) => {
        if (err) {
            res.status(500).json(err);
        }
        else {
            res.json(result);
        }
    });
    res.send('OK');

});

app.get('/webhooks/inbound-sms', (req, res) => {
    const from = req.query.msisdn;
    const to = req.query.to;
    const text = req.query.text;

    // Route virtual number to real number
    smsProxy.proxySms(from, text);

    res.sendStatus(204);
});
```

### Configure your application

Create a .env file along side your server and add the following to it with the appropriate values filled in:

```
NEXMO_API_KEY=NEXMO_API_KEY
NEXMO_API_SECRET=NEXMO_API_SECRET
VIRTUAL_NUMBER=VIRTUAL_NUMBER
```

### Configure your webhooks and fire up ngrok

Next Configure your webhooks in the dashboard or via the CLI to point at the /webhooks/inbound-sms url. Then Start up ngrok `ngrok http 3000` 

### Start your App and Create a Chat

Start the app with `node server.js`

Create a chat with `curl -X POST   'http://localhost:3000/chat?userANumber=UserANumber&userBNumber=UserBNumber'` - you can use postman if you do not have curl.