---
title: "Call Your Application"
weight : 25
---

**Make a phone call, have your application answer it**

To receive incoming calls, you need a publicly-available URL that will respond with a NCCO telling Nexmo what to do with the call. One thing that's different from the previous example is that it does not need an API keys - the Nexmo server will call your application this time rather than the other way around!

1. Start by adding a simple route to your application, the code samples here are to help you get started, or you can of course improvise.

    **JavaScript**

    Prepare your dependencies: `npm install nexmo express`

    ```js
    const app = require('express')()

    const onInboundCall = (request, response) => {
    const from = request.query.from
    const fromSplitIntoCharacters = from.split('').join(' ')

    const ncco = [{
        action: 'talk',
        text: `Thank you for calling from ${fromSplitIntoCharacters}`
    }]

    response.json(ncco)
    }

    app.get('/webhooks/answer', onInboundCall)

    app.listen(3000)
    ```

    Save the code sample to `index.js` and then run `node index.js`.

    **PHP**

    This example uses the Slim Framework (v3) microframework for some lightweight input/output handling and routing. To include this in your project, use Composer:

    `composer require nexmo/client slim/slim:3.11`

    With the dependencies in place, here's some code to give a `/webhooks/answer` endpoint that returns an NCCO:

    ```php
    <?php
    use \Psr\Http\Message\ServerRequestInterface as Request;
    use \Psr\Http\Message\ResponseInterface as Response;

    require 'vendor/autoload.php';

    $app = new \Slim\App;
    $app->get('/webhooks/answer', function (Request $request, Response $response) {
        $params = $request->getQueryParams();
        $fromSplitIntoCharacters = implode(" ", str_split($params['from']));

        $ncco = [
            [
                'action' => 'talk',
                'text' => 'Thank you for calling from '.$fromSplitIntoCharacters
            ]
        ];

        return $response->withJson($ncco);
    });

    $app->run();
    ```

    Save the code into `index.php`. Then try using the built-in PHP webserver to serve the code:

    `php -S localhost:3000`

    Again, there are [code examples in other languages](https://developer.nexmo.com/voice/voice-api/code-snippets/receive-an-inbound-call) if you prefer.

2. **Test your endpoint** by requesting <http://localhost:3000/webhook/answer> in your browser. You should see some JSON returned.

3. Next, your local URL needs to be publicly available. One way to do this is to use [ngrok](https://ngrok.com):

    `ngrok http 3000`

    When ngrok starts the tunnel, it will show you your URL, something like `https://abc123.ngrok.io` - copy the URL from your ngrok console as we will need it shortly.

4. Back in the dashboard, you can edit the Answer URL of your application, by pasting the ngrok URL and adding `/webhooks/answer` to the end of it, to make something like `https://abc123.ngrok.com/webhooks/answer`.

5. **Call your application** by making a call from your cellphone to the Nexmo number linked to your application. You should hear the spoken greeting giving the number you are calling from.

### Next Steps: A more interesting greeting

We've shared our standard "answer a call" example but it isn't particularly interesting! What do you wish your application would do? How about:

* A talking clock
* A randomly chosen uplifting message
* Some music

Use the [NCCO reference documentation](https://developer.nexmo.com/voice/voice-api/ncco-reference) to try some alternative content for your Answer URL.

For more ideas, there's a set of [NCCO Examples on GitHub](https://github.com/nexmo-community/ncco-examples) that might help you along the way.

