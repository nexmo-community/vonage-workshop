---
title: "Voice Proxying"
weight : 65
---

For marketplace scenarios such as food delivery or taxi and passenger communications, it is useful for users to be able to communicate with one another without exposing their phone numbers. By implementing voice proxying with Nexmo's Voice API you ensure that your users cannot bypass the required or preferred communication workflows and audits.

## Create Nexmo App

Let's start off by creating a new nexmo-app to manage the voice proxying. Using the CLI let's `nexmo app:create voice-proxy https://example.com/proxy-call` replace example.com with your base-url

The parameters are:

* voice-proxy - the name you give to this application
* https://example.com/proxy-call - when you receive an inbound call to your virtual number, Nexmo makes a GET request and retrieves the NCCO that controls the call flow from this webhook endpoint
* https://example.com/event - as the call status changes, Nexmo sends status updates to this webhook endpoint

## Build the App

### Setup web server

Now let's create a webserver we'll host the app in. Create a new folder called voice-proxy. Cd into it and run `npm install bluebird nexmo express body-parser`.

Create a file, server.js and add this code to it to create your webserver:

```js
"use strict";

var express = require('express');
var bodyParser = require('body-parser');

var app = express();
app.set('port', (process.env.PORT || 3000));
app.use(bodyParser.urlencoded({ extended: false }));

var config = require('./config');
console.log(config)
var VoiceProxy = require('./VoiceProxy.js');
var voiceProxy = new VoiceProxy(config);

voiceProxy.createConversation(config.USERA_NUMBER, config.USERB_NUMBER,(err,res)=>{
    console.log(res)
})

app.listen(app.get('port'), function() {
  console.log('Voice Proxy App listening on port', app.get('port'));
});
```

### Provisioning Numbers

If there are extra numbers needed simply go to the [dashboard](https://dashboard.nexmo.com/) to get another number. Alternately they can be rented via the CLI.

Let's add those numbers to our config as FIRST_NUMBER, and SECOND_NUMBER.

### Create the voice proxy class

Next let's create a VoiceProxy.js class and in that class create a constructor that simply takes a config parameter

```js
const Nexmo = require('nexmo')
const Promise = require('bluebird')
class VoiceProxy{
    /**
     * Create a new VoiceProxy
     */
    constructor(config) {
        this.config = config;
    
        this.nexmo = new Nexmo({
            apiKey: config.NEXMO_API_KEY,
            apiSecret: config.NEXMO_API_SECRET
        },{
            debug: this.config.NEXMO_DEBUG
        });
    
    
        // Virtual Numbers to be assigned to UserA and UserB
        this.provisionedNumbers = [config.FIRST_NUMBER, config.SECOND_NUMBER];
        console.log(this.provisionedNumbers);
    
        // In progress conversations
        this.conversations = [];
    };
```

#### Create a Call

The next step is to actually create a call. This still will.

* Validate the Phone Numbers to be used in the call
* Maps the proxy numbers to the real phone numbers
* Send an SMS to each party of the call to tell them what number to call into

this will inevitably culminate in this bit of code
```js
/**
 * Create a new tracked conversation so there is a real/virtual mapping of numbers.
 */
createConversation(userANumber, userBNumber, cb) {
    this.checkNumbers(userANumber, userBNumber)
    .then(this.saveConversation.bind(this))
    .then(this.sendSMS.bind(this))
    .then(function(conversation) {
        cb(null, conversation);
    })
    .catch(function(err) {
        cb(err);
    });
};
```

#### Number Validation

The first step of this process is actually validating that the numbers are of the correct country - this can be done with a basic NumberInsight call

```js
/**
 * Ensure the given numbers are valid and which country they are associated with.
 */
checkNumbers(userANumber, userBNumber) {
    let niGetPromise = Promise.promisify(this.nexmo.numberInsight.get, {context: this.nexmo.numberInsight});
    let userAGet = niGetPromise({level: 'basic', number: userANumber});
    let userBGet = niGetPromise({level: 'basic', number: userBNumber});

    return Promise.all([userAGet, userBGet]);
};
```

#### Map the Virtual to Real Numbers

With the knowledge that the numbers are indeed valid we can move onto mapping the virtual numbers onto the real numbers:

```js
/**
 * Store the conversation information.
 */
saveConversation(results) {
    let userAResult = results[0];
    let userANumber = {
    msisdn: userAResult.international_format_number,
    country: userAResult.country_code
    };

    let userBResult = results[1];
    let userBNumber = {
    msisdn: userBResult.international_format_number,
    country: userBResult.country_code
    };

    // Create conversation object - for demo purposes:
    // - Use first indexed LVN for user A
    // - Use second indexed LVN for user B
    let conversation = {
    userA: {
        realNumber: userANumber,
        virtualNumber: this.provisionedNumbers[0]
    },
    userB: {
        realNumber: userBNumber,
        virtualNumber: this.provisionedNumbers[1]
    }
    };

    this.conversations.push(conversation);

    return conversation;
};
```

#### Send Prompting SMS

With the virtual and real numbers mapped, the next step is to prompt the users in the call to actually call into the appropriate number, to this end we'll send a prompting SMS to each user with the appropriate Number for them to call into.

```js
/**
 * Send an SMS to each conversation participant so they know each other's
 * virtual number and can call either other via the proxy.
 */
sendSMS(conversation) {
    // Send UserA conversation information
    // From the UserB virtual number
    // To the UserA real number
    this.nexmo.message.sendSms(conversation.userB.virtualNumber,
                            conversation.userA.realNumber.msisdn,
                            'Call this number to talk to UserB');

    // Send UserB conversation information
    // From the UserA virtual number
    // To the UserB real number
    this.nexmo.message.sendSms(conversation.userA.virtualNumber,
                            conversation.userB.realNumber.msisdn,
                            'Call this number to talk to UserA');

    return conversation;
};
```

The Users won't be able to send SMS's to each other - rather they will need to dial into the number provided to be connected to the other party.

#### Reverse Map Real to Virtual Numbers

Now that we know which numbers are dialing in and which numbers to map them to we need to reverse map the inbound numbers to the appropriate virtual number and make the call

```js
let fromUserAToUserB = function(from, to, conversation) {
  return (from === conversation.userA.realNumber.msisdn &&
          to === conversation.userB.virtualNumber.msisdn);
};
let fromUserBToUserA = function(from, to, conversation) {
  return (from === conversation.userB.realNumber.msisdn &&
          to === conversation.userA.virtualNumber.msisdn);
};

/**
 * Work out real number to virtual number mapping between users.
 */
VoiceProxy.prototype.getProxyRoute = function(from, to) {
  let proxyRoute = null;
  let conversation;
  for(let i = 0, l = this.conversations.length; i < l; ++i) {
    conversation = this.conversations[i];

    // Use to and from to determine the conversation
    let fromUserA = fromUserAToUserB(from, to, conversation);
    let fromUserB = fromUserBToUserA(from, to, conversation);

    if(fromUserA || fromUserB) {
      proxyRoute = {
        conversation: conversation,
        to: fromUserA? conversation.userB : conversation.userA,
        from: fromUserA? conversation.userA : conversation.userB
      };
      break;
    }
  }

  return proxyRoute;
};
```

#### Producing an NCCO For the inbound call

Next we'll have to produce an NCCO for our webhook to return when the answer url is hit this will produce a talk action indicating they are being connected to the other party, and a connect action to actually make the call to the other party:

```js
getProxyNCCO(from, to){
    var route = this.getProxyRoute(from,to);
    var ncco = [
        {
            action:'talk',
            text:'Connecting you with the other party'
        },
        {
            action:'connect',
            from:route.from.virtualNumber,
            timeout:"30",
            endpoint: [
                {
                    type:'phone',
                    number:route.to.realNumber.msisdn

                }
            ]
        }
    ]
    return ncco;
}
```

#### Handling the inbound call

And at last, we get to the actual handling of the call, with everything setup in VoiceProxy.js, turn back to server.js and add the following code - this will manage the webhook

```js
app.get('/proxy-call', function(req, res) {
  var from = req.query.from;
  var to = req.query.to;

  var ncco = voiceProxy.getProxyNCCO(from, to);
  res.json(ncco);
});
```

#### configuration

we just use a simple config.json file to configure this, alongside server.js create config.json and populate it like so

```text
{
    "FIRST_NUMBER":"FIRST_NUMBER",
    "SECOND_NUMBER":"SECOND_NUMBER",
    "NEXMO_API_KEY":"NEXMO_API_KEY",
    "NEXMO_API_SECRET":"NEXMO_API_SECRET",
    "USERA_NUMBER":"USERA_NUMBER",
    "USERB_NUMBER":"USERB_NUMBER"
}
```

### And thats it!

With that your app should be all set. run `node server.js` to run and you'll be off to the races