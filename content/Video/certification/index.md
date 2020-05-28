---
title: "Certification Project"
weight : 1
---

## Video API Certification

### Objective

This is a guided activity that provides you with some initial starting code and relies on you to complete it. The solution is in two parts - the server (written in Node.js using the Express framework) and the client, which uses vanilla JavaScript.

After successfully completing the activity, you will be able to create a single participant video session and see yourself in your web cam. You can spin up multiple client instances and all will connect to the same session and therefore see the same video.

> If you haven't already, create an account at [Tokbox](https://tokbox.com) and receive your $10 free credit.


### Get started
We have created an incomplete project for you. This is available in a [GitHub repository](https://github.com/nexmo-se/cst-training-emea/tree/master/video).

If you completed the activity for the Conversation API, you already have the files you need and they are located in the `cst-training-emea/video/` directory.

If not, then clone the GitHub repository as follows:

- Using SSH:

  ```
  git clone git@github.com:nexmo-se/cst-training-emea.git
  ```

- Using HTTPS:

  ```
  https://github.com/nexmo-se/cst-training-emea.git
  ```

Or, if you are unable to use `git` for this, click the green "Clone or download" button to download the source code as a zip file and extract it on your local machine.

### The Server

You'll need to complete 4 steps to build the server. Once completed, your server will be able to:

1. Generate a session
2. Generate tokens for the session
3. Receive the session events
4. Whatever else you decide to enable it to do!

The server skeleton is located in `./application_server` and the steps you need to complete are described in the comments of the `routes/tkbx.js` and `routes/events.js` files

To begin, install the server:

```
npm install
```

To run the server, execute:

```
npm run start
```

By default the server uses port 3000.

> NOTE: The server will only create one session per run, which means that all clients that fetch the session will be connected to the same one. This will enable you see yourself from two different clients.

You can test your server using Postman or cURL.

To expose your server to the outside world, we recommend using [ngrok](basic-concepts/ngrok).

### The Client

The skeleton for the tutorial is in the `web_client` directory.

Please follow [this tutorial](https://tokbox.com/developer/tutorials/web/basic-video-chat/) to flesh out the skeleton, but with the following changes:

Your `API_KEY` should be hardcoded. You can retrieve this from the [Developer Dashboard](https://dashboard.nexmo.com).

You should generate the session and token using the server you've just built. Use [this article](https://www.freecodecamp.org/news/here-is-the-most-popular-ways-to-make-an-http-request-in-javascript-954ce8c95aaa/#jquery-methods) for a quick explantation on how to perform HTTP requests. The project already contains Ajax support.

The flow should be as follows:

1. Create the session
2. Create the token
3. Initialize the session locally

Complete the rest of the tutorial as described in the code comments. 

### Delivery

Please see [here](/basic-concepts/certification/) for details of how to submit your completed project.