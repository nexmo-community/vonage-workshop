---
title: "Record a Call"
weight : 55
---

Now that we have successfully implemented the basic IVR we still have the matter of the press office to attend to. No one is currently present from the press office to handle our call so we are going to need to allow for a mechanism for folks calling into our IVR to leave a message for the press office.

We're going to do that by allowing them to leave a message with the press office.

Remember from the onInput callback from the previous section - the switch for input 3. 

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

## Adding a record action

We are going to edit this snippet of code to actually instruct Nexmo to preform the voice recording for us. To do this, we are going to add another action. Let's modify the snippet of code above to hold this record action. 

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

Notice how the talk action now tells caller to leave a message, as well as the new record action.

The record action provides an eventUrl, which is the webhook we are going to receive a notification on when the call finishes recording. It also has beepStart set to true, which will beep just prior to thee start of the recording, and it has an endOnSilence parameter set to 3, this will end the recording after 3 seconds of silence is detected.

## Adding a recordingFinished webhook

The final stop we need to take here is to add the recordingFinished webhook logic to our app, and to register a route for it.

### Register the new route

Modify the app code that we saw earlier from:

```js
app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)
  .post('/webhooks/events', onEvent)
  .post('/webhooks/accountInput', onAccountInput)
```

To

```js
app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)
  .post('/webhooks/events', onEvent)
  .post('/webhooks/accountInput', onAccountInput)
  .post('/webhooks/recordingFinished', onRecordingFinished)
```

### Add the route complete handler

The next thing to do is to add the onrecordingFinished method to our server for the moment we'll just print out the URL where the recording wound up.

```js
const onRecordingFinished = (request, response) =>{
    console.log(request.body.recording_url)
    response.status(200).send();
}
```

Now you can run `node index.js` again and call into your nexmo number, press 3 and leave a message for the press office

## Retrieving a recording

Now that you have actually made the recording the next logical question is 'well how do I retrieve it?' As you could probably imagine this will involve the url that we just printed above. The Nexmo Client will actually enable us to pull down the recording. Let's harken back to the (first call)[/first-call].

We will need to first require the nexmo client so let's add `const Nexmo = require('nexmo')` to the top of the index.js file

Next we'll initialize it like we did in the first call section

```js
const nexmo = new Nexmo({
  apiKey: NEXMO_API_KEY,
  apiSecret: NEXMO_API_SECRET,
  applicationId: NEXMO_APPLICATION_ID,
  privateKey: NEXMO_APPLICATION_PRIVATE_KEY_PATH
})
```

with this complete let's now modify our onRecordingFinished to actually preform the save logic:

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

With this we are now able to have someone call in, press 3 to speak with the press office, which then prompts them to leave a voicemail with the press office, and to download that voicemail.