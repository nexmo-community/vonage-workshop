---
title: "Verify API"
weight : 3
---

## Verify API

The Verify API enables you to perform two factor authentication (2FA) using SMS and text-to-speech phone calls (TTS). This gives you confidence that the number a person uses to register for your service actually belongs to them.

The typical process for using the Verify API is as follows:

* You [create the Verify request](/verify/create-verify-request) - using your preferred workflow
* Vonage's API platform sends a PIN to the user by either SMS, text-to-speech phone call or both, depending on the workflow
* You provide a way for the user to enter the PIN that they received into your application and submit a [check request](/verify/check-verify-request) to determine if the number entered is the one that was sent. 

You can also use the Verify API to [cancel a request](/verify/cancel-verify) or [advance to the next verification attempt](/verify/advance-verify-request) in the workflow.
