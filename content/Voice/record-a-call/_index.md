---
title: "Record a Call*"
weight : 55
hidden: true
---

## Overview

Now that we have successfully implemented the basic IVR we still have the matter of the press office to attend to. No one is currently present from the press office to handle our call so we are going to need to allow people calling into our IVR to leave a message for the press office.


## Recording the call

Remember from the `onInput` webhook from the previous section - the switch for input 3?

```js
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
```

We are going to edit this code to instruct the Voice API to preform the voice recording for us. To do this, we are going to add a `record` action. Modify `ivr.js` from the previous exercise as follows:

```js
        case "3":
            ncco =
            [
                {
                    action: 'talk',                    
                    text: 'You have asked to speak with the press office. Unfortunately no one from the press office is currently available. Please leave a message after the tone.'
                },
                {
                    action: 'record',
                    beepStart: true,
                    eventUrl: [`${base_url}/webhooks/recordingFinished`],
                    endOnSilence: 3
                }
            ]
            response.json(ncco)
            break;
```

Note how the `talk` action now tells the caller to leave a message, as well as the new `record` action.

The `record` action provides an `eventUrl`, which is the webhook we are going to receive a notification on when the call finishes recording. It also has `beepStart` set to `true`, which will beep just prior to the start of the recording. The`endOnSilence` parameter is set to `3`, which will end the recording after three seconds of silence.

## Processing the recording

The final stop we need to take here is to add the `recordingFinished` webhook logic to our app, and to register a route for it.

### Register the new route

Add a new route for the `recordingFinished` webhook:

```js
app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)
  .post('/webhooks/events', onEvent)
  .post('/webhooks/accountInput', onAccountInput)
  .post('/webhooks/recordingFinished', onRecordingFinished)
```

### Add the route handler

The next thing to do is to add the `onrecordingFinished` method to our server. For now, we'll just display the URL where the recording is located:

```js
const onRecordingFinished = (request, response) =>{
    console.log(request.body.recording_url)
    response.status(200).send();
}
```

Now you can run `node ivr.js` again and call your virtual number, press 3 and leave a message for the press office.

## Retrieving a recording

Now that you have actually made the recording the next logical question is 'well how do I retrieve it?' As you could probably imagine this will involve the URL that we just displayed. The server SDK will actually enable us to pull down the recording, but we need to install and initialise it first.

Run `npm install nexmo` to install the `nexmo` package dependency.

Then `require` it by adding the following line to the top of the `ivr.js` file:

```js
const Nexmo = require('nexmo')
```

Next we'll initialize it like we did when we made an outbound call.

```js
const nexmo = new Nexmo({
  apiKey: NEXMO_API_KEY,
  apiSecret: NEXMO_API_SECRET,
  applicationId: NEXMO_APPLICATION_ID,
  privateKey: NEXMO_APPLICATION_PRIVATE_KEY_PATH
})
```

Just hardcode these values for now, to save time.

Then, modify our `onRecordingFinished` route hander to save the recording from the URL sent to our webhook:


```js
const onRecordingFinished = (request, response) =>{
    console.log(request.body.recording_url)
    nexmo.files.save(request.body.recording_url,'test.mp3',(err, res) => {
        if(err) { console.error(err); }
        else {
            console.log(res);
        }
      });
    response.status(200).send();
}
```

## Run it!

We're now good to go. Run `node ivr.js` and then call your virtual number. Press 3 to speak to the press office.

## Outcome

You will be prompted to leave a voicemail and, once complete, it will be downloaded in the `test.mp3` file in your project directory.

## Alternative Languages

See the following examples in the docs:

* [Record a message](https://developer.nexmo.com/voice/voice-api/code-snippets/record-a-message)
* [Download a recording](https://developer.nexmo.com/voice/voice-api/code-snippets/download-a-recording)

Click through to see the full source code on GitHub.