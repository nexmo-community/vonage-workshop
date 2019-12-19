---
title: "Verify API"
weight : 3
---

## Verify API

The Verify API enables us to preform two factor authentication over telephony services such as Phone Calls and SMS. 

The typical flow of a Verify API call will consist of 

* Create Verify request with Nexmo - using one of several workflows
* Nexmo reaches out to the identified party with a pin number, depending on the Workflow the user may be contacted several diffent ways depending on response time
* User Gives back to application the pin number provided by Nexmo and submits a check request
* If the pin number supplied matches the Verify request's pin - the request is accepted
