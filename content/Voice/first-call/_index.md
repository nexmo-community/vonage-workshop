---
title: "Make Your First Call"
weight : 15
---

**Write some code that calls your cellphone**

You will need:

* A Nexmo account if you don't have one already. Sign up here: <https://dashboard.nexmo.com/sign-up>
* A Nexmo phone number to make calls with. You can [check your existing numbers](https://dashboard.nexmo.com/your-numbers) and [buy numbers](https://dashboard.nexmo.com/buy-numbers) on the dashboard.
* An application - you need both the application UUID and the private key file copied somewhere safe. From the [dashboard](https://dashboard.nexmo.com), visit "Voice" and then "Create an application". Give your new application a name and choose "Generate public and private key"; your browser will download the private key. Set your application to have Voice capabilities (you can use `example.com` URLs for now, we will update these later) and save it. Once you have created the application, link the Nexmo number you will use.
* A number you can phone (probably your cellphone).
* Some sort of working tech stack. Our examples are NodeJS and PHP but you should feel free to use whatever technology you know how to make API calls with!

Optional, but recommended:

* The [Nexmo CLI tool](https://developer.nexmo.com/tools) may be a nicer way to work with the number purchase, application creation, etc.
* The [Server SDK](https://developer.nexmo.com/tools) for your tech stack - we have PHP, Python, Ruby, NodeJS, Java, .NET (and a semi-official [Go SDK](https://github.com/nexmo-community/nexmo-go))

Here's the code to get you started, replace the placeholder values in your chosen code:

 * `NEXMO_APPLICATION_PRIVATE_KEY_PATH`: The path to the private key file you saved when creating the application
 * `NEXMO_APPLICATION_ID`: The UUID of your application
 * `NEXMO_NUMBER`: Your Nexmo number that the call will be made from. For example `447700900000`.
 * `TO_NUMBER`: The number you would like to call to in [E.164](https://en.wikipedia.org/wiki/E.164) format. For example `447700900001` (note that this _must_ include the dialling code, so if it's a US number, it should start with `1`).

**Javascript**

Prepare your dependencies: `npm install nexmo`

```js
const nexmo = new Nexmo({
  apiKey: NEXMO_API_KEY,
  apiSecret: NEXMO_API_SECRET,
  applicationId: NEXMO_APPLICATION_ID,
  privateKey: NEXMO_APPLICATION_PRIVATE_KEY_PATH
})

nexmo.calls.create({
  to: [{
    type: 'phone',
    number: TO_NUMBER
  }],
  from: {
    type: 'phone',
    number: NEXMO_NUMBER
  },
  ncco: [{
    "action": "talk",
    "text": "This is a text to speech call from Nexmo"
  }]
})
```

**PHP**

Prepare your dependencies: `composer require nexmo/client`

```php
<?php
require 'vendor/autoload.php';

$keypair = new \Nexmo\Client\Credentials\Keypair(
    file_get_contents(NEXMO_APPLICATION_PRIVATE_KEY_PATH),
    NEXMO_APPLICATION_ID
);
$client = new \Nexmo\Client($keypair);

$call = $client->calls()->create([
    'to' => [[
        'type' => 'phone',
        'number' => TO_NUMBER
    ]],
    'from' => [
        'type' => 'phone',
        'number' => NEXMO_NUMBER
    ],
    'ncco' => [
        [
            'action' => 'talk',
            'text' => 'This is a text to speech call from Nexmo'
        ]
    ]
]);

print_r($call);
```

Put this code into `index.php`, and run it with `php -f index.php`.

Check out the [code examples in these and other languages](https://developer.nexmo.com/voice/voice-api/code-snippets/make-an-outbound-call) on the Nexmo Developer Portal.

**Run your code** and answer your phone!

### Next Steps: Customise your call

What would you like to hear? Check out our [NCCO reference documentation](https://developer.nexmo.com/voice/voice-api/ncco-reference) to learn what else you can do, if you'd like your spoken greeting to have a bit more expression you can investigate [SSML (Speech Synthesis Markup Language)](https://developer.nexmo.com/voice/voice-api/guides/customizing-tts).

### Next Steps: Track events (insight and debugging tactic)

Go back to the dashboard and configure the application's `event_url` endpoint. You can either point this to:

* your application (probably using [ngrok](https://ngrok.com)), we'll be doing another incoming webhook in the next exercise anyway
* a tool such as the [Voice Event Logger](https://github.com/Nexmo/voice-event-logger)
* a general webhook receiver like Requestbin (still available at <http://bin.on.dockerize.io/> and <http://requestbin.net>) or [Postbin](https://postb.in/)

