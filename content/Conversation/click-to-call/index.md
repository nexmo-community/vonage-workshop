---
title: "Create a Click-to-call web app"
weight : 999
hidden: false
---

In this tutorial, you will learn how to create a Client SDK application and then write the code to make a phone call from your Web App to a PSTN phone.

To achieve this, you will need:

- A web application that will make the voice call using the Client SDK
- A webhook server that can process an inbound webhook and return an NCCO to forward the inbound voice call to the destination phone

## Steps

### 1. Clone the GitHub repository

We have created an incomplete project for you. This is available in a [GitHub repository](https://github.com/nexmo-se/cst-training-emea/tree/master/voice).

Clone the GitHub repository as follows:

- Using SSH:

  ```
  git clone git@github.com:nexmo-se/cst-training-emea.git
  ```

- Using HTTPS:

  ```
  https://github.com/nexmo-se/cst-training-emea.git
  ```

Or, if you are unable to use `git` for this, click the green "Clone or download" button to download the source code as a zip file and extract it on your local machine.

Note that this GitHub repository contains sample code for both the Conversation and Video workshops. Navigate to the `cst-training-emea/voice` directory of the repository before continuing.

### 2. Create a Nexmo Application

First, ensure that you have the **beta** version of the Nexmo CLI Installed. If you attended the Messages and Dispatch APIs workshop, then you installed this as part of that activity. If not, install it using `npm` as follows:

```
npm install nexmo-cli@beta -g
```

The Nexmo CLI should read your API key and secret from previous installations of the CLI. This information is stored in `~/.nexmorc`. If this file is not present, then you need to set up the Nexmo CLI manually, by using the following command:

```
nexmo setup YOUR_API_KEY YOUR_API_SECRET
```

Then, create a Nexmo application interactively. The following command enters interactive mode:

```
nexmo app:create
```

Complete each prompt using the following information:

- Application Name: `click-to-call`
- Capabilities: Select both `voice` and `rtc`
- Use default HTTP methods? `Y`
- Voice Answer URL: Press return - you will configure this later
- Voice Fallback URL: Press return
- Voice Event URL: Press return
- RTC Event URL: Press return
- Public key path: Delete placeholder text and press return

The application is then created.

The file `.nexmo-app` is created in your project directory. This file contains the Nexmo Application ID and the private key. Please take the time to review the content of this file.

Note the private key and copy it, in its entirety, to a file called `private.key` in the same directory, including the `---BEGIN PRIVATE KEY---` and `---END PRIVATE KEY---` lines.

You can obtain information about your application, including the Application ID, in the Nexmo Dashboard (https://dashboard.nexmo.com/voice/your-applications).

Visit the Dashboard now and verify that your application has been created. Then, click on your new application and link your virtual number to it from the list of numbers at the bottom of the page.


### 3: Create a User 

[Users](https://developer.nexmo.com/conversation/concepts/user) are a key concept when working with the Nexmo Client SDKs. When a user authenticates with the Client SDK, the credentials provided identify them as a specific user. Each authenticated user will typically correspond to a single user in your users database.

``` 
nexmo user:create name="MY_USER_NAME"
```

Use any suitable name for `MY_USER_NAME`.

Make a note of the User ID that is returned as a result of executing this command.

### 4: Generate a JWT for authentication

The Client SDK uses JWTs for authentication. The JWT identifies the user name, the associated application ID and the permissions granted to the user. It is signed using your private key to prove that it is a valid token.

**Note**: Remember to replace the `MY_APP_ID` and `MY_USER_NAME` variables with your own values in the sections below.

```

nexmo jwt:generate ./private.key sub=MY_USER_NAME exp=$(($(date +%s)+21600)) acl='{"paths":{"/*/users/**":{},"/*/conversations/**":{},"/*/sessions/**":{},"/*/devices/**":{},"/*/image/**":{},"/*/media/**":{},"/*/applications/**":{},"/*/push/**":{},"/*/knocking/**":{}}}' application_id=MY_APP_ID

```

Make a note of the JWT that is returned by this command.

Well done. Now it's time to implement webhooks :)

### 5. Run ngrok

For the webhooks that you're going to develop as part of this activity to be available to Vonage's APIs, they need to be accessible over the public Internet. You can achieve this by creating a secure tunnel using `ngrok`, which you installed earlier.

Run `ngrok` on port 3000 using the following command:

```
./ngrok http 3000
```

The URL that `ngrok` provides will form the domain part of your webhooks, which you will configure in the Developer Dashboard in a later step. Keep `ngrok` running for the duration of this exercise. If you restart `ngrok`, the domains will change. For more information on `ngrok`, see [our guide](/basic-concepts/ngrok).

### 6. Implement the server 

The repository includes a file called `server.js` that contains the initial code. The comments provide instructions for you to complete the remaining code. Please complete these steps in the order given and refer to the placeholder comments for each of the steps. For example, the initial Step 1 comment tells you that you need to implement webhooks. Look for other Step 1 comments further down the file to see where in the code you need to put these.

When instructed to edit the client code, make your changes to `index.html` instead.

```javascript
const path = require('path');
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

app.use(express.static('node_modules/nexmo-client/dist'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));


/* STEP 1

Nexmo will make an HTTP request to your answer_url when an inbound call is received.

You need to create a webhook server that is capable of receiving this request 
and returning an NCCO containing an action (https://developer.nexmo.com/voice/voice-api/ncco-reference).

As a first step, create an ncco with a talk action. Call the LVN number and check if it's working.

NOTE: Please remember to set up application webhook in the Nexmo Dashboard (https://dashboard.nexmo.com/applications)

*/


/* STEP 2

Implement VAPI Event Webhook & RTC Event Webhook. Log the request body using console.log and return 200 OK
Make sure to configure webhooks in your dashboard.

Doc: https://developer.nexmo.com/voice/voice-api/webhook-reference#event-webhook

*/

/* STEP 3

Go to the client side and complete the task before.

Once you've completed client side, you need to edit the NCCO to forward the call to a PSTN number.

Please check the Answer Webhook body, you will have to extract the PSTN Number to connect to.

Implement a logic to allow calls only to allowed numbers (for example, only to your personal number).
If the number is in the allowed numbers, the call is forwarded to it.
Otherwise, implement a TTS action with an alert to the user (for example: Sorry, that number is not permitted).


Doc: https://developer.nexmo.com/voice/voice-api/ncco-reference

*/


// STEP 1 - VAPI ANSWER WEBHOOK
// Type the code below this point please
app.get('/webhooks/answer', (req, res) => {
	console.log("Answer:");
	console.log(req.query);
	var destNumber = null; // How do you get the number from req.query? 
	const ncco = []
	res.json(ncco);
});

// STEP 1 - VAPI EVENT WEBHOOK - INIT
// Type the code below this point please



// STEP 1 - RTC EVENT WEBHOOK - INIT
// Type the code below this point please




app.get('/', function (req, res) {
	res.sendFile(path.join(__dirname + '/index.html'));
});

app.listen(port, () => console.log(`Server listening on port ${port}!`));

```

### 7. Review the client code

The client uses the Nexmo SDK to call the PSTN number. The method used is [Call Server](https://developer.nexmo.com/sdk/stitch/javascript/Application.html#callServer__anchor).

Review the client code in `index.html` and ensure that you understand how it works.


### Run it!

Firstly, make sure you have installed the required node modules by running `npm install` inside the `voice` folder.

Then, to run the server in development mode, run:

```
npm run dev
```

The script uses `livereload`, so you don't need to reload the script everytime you make changes to the file.

Launch the `index.html` file to see your application in action!


## Taking it further

For bonus points, implement the [Hang Up Method](https://developer.nexmo.com/sdk/stitch/javascript/NXMCall.html#hangUp__anchor).


## Resources

- [Client SDK documentation](https://developer.nexmo.com/sdk/stitch/javascript/index.html)