---
title: "Interact with your user"
weight : 35
---


Making a call, or receiving one, is great but what next? In this final exercise we'll interact with our user. To keep things simple, we'll prompt them to input digits on their keypad, but the same approaches would work with spoken interactions on both sides.

The outline of the process looks something like this:

1. Create an NCCO to prompt the user to input some digits by using a [`talk`](https://developer.nexmo.com/voice/voice-api/ncco-reference#talk) action and then an [`input`](https://developer.nexmo.com/voice/voice-api/ncco-reference#input) action. The `input` action accepts a URL so use your ngrok URL with a suffix - our examples use `/webhooks/dtmf`.
2. When the user sends some input, we'll receive a webhook containing some data to identify the call (see the [detail of the webhook](https://developer.nexmo.com/voice/voice-api/webhook-reference#input) on the Developer Portal)
3. Using the data that arrived in the webhook, we can return a new NCCO with custom content.

Start a new application, we have some new code for you!

**JavaScript**

Prepare your dependencies: `npm install nexmo express body-parser`

```js
const app = require('express')()
const bodyParser = require('body-parser')

app.use(bodyParser.json())

const onInboundCall = (request, response) => {
  const ncco = [
    {
      action: 'talk',
      text: 'Please enter a digit'
    },
    {
      action: 'input',
      eventUrl: [`${request.protocol}://${request.get('host')}/webhooks/dtmf`]
    }
  ]

  response.json(ncco)
}

const onInput = (request, response) => {
  const dtmf = request.body.dtmf

  const ncco = [{
    action: 'talk',
    text: `You pressed ${dtmf}`
  }]

  response.json(ncco)
}

app
  .get('/webhooks/answer', onInboundCall)
  .post('/webhooks/dtmf', onInput)

app.listen(3000)
```

Put this code into `index.js` and start the server with `node index.js`.

**PHP**

Again, there is a Slim Framework dependency so use `composer require slim/slim:3.11` to bring that into your project.

```php
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require 'vendor/autoload.php';

$app = new \Slim\App;

$app->get('/webhooks/answer', function (Request $request, Response $response) {
    $uri = $request->getUri();
    $ncco = [
        [
            'action' => 'talk',
            'text' => 'Please enter a digit'
        ],
        [
            'action' => 'input',
            'eventUrl' => [
                $uri->getScheme().'://'.$uri->getHost().':'.$uri->getPort().'/webhooks/dtmf'
            ]
        ]
    ];

    return $response->withJson($ncco);
});

$app->post('/webhooks/dtmf', function (Request $request, Response $response) {
    $params = $request->getParsedBody();

    $ncco = [
        [
            'action' => 'talk',
            'text' => 'You pressed '.$params['dtmf']
        ]
    ];

    return $response->withJson($ncco);
});

$app->run();
```

Place this code in `index.php`, then use the built-in webserver:

`php -S localhost:3000`

**Run your code** - if your ngrok server is still running from the previous example then you should be able to simply call your Nexmo number from your cellphone again. Don't forget to bring up the dial pad ready to enter a digit!
