---
title: "Twitter Dm Api"
date: 2021-06-16T11:30:04+05:30
draft: true
---

## Introduction

A common problem, that we face during taking inputs from user, for their social media account in any app, is the data integrity, we dont have any solid solutions to verify the integrity of their account. The user could provide some random userId, or can provide someone else's userId, this could then create an loop hole in our app, and can hamper the data intergrity of the app, so to avoid this, we can add some form of validation, to validate the user accocunt.

## How can we solve the problem arised ?

One such solution to the above arised problem, could be solved with the concept of One Time Passwords. Just like you receive an OTP in you mobile device, while confirming your mobile number, if there could be a way, you recieve an OTP in your social media accounts too, then this problem could be solved. You then need to enter this OTP, in the app/platform which sent you the OTP. This way the app/platform could verify if the userId which you entered in their platform, belong to you hence preserving the intergrity of their data.

## Solving the above problem using Twitter API

Now comes the question, how can you send OTP or any such message programatically to the user. Lets see how can we do that in the twitter.
Say i want my user to enter his/her twitter Id, in my app, and then also want to check if the entered twitterId belongs to the same person. To verify this i will send a message to the user in his/her twitter account, with an OTP and make him/her enter the same in my platform. So to do that, i will need to see if theres an available functionality in the Twitter API to do so.

Step 1: Apply for a twitter [Developer account](https://developer.twitter.com/en/apply-for-access)

Step 2: Head over to the twitter [Developer docs](https://developer.twitter.com/en/docs) and search for Direct Messages

Step 3: You can select the [Direct Message API](https://developer.twitter.com/en/docs/twitter-api/v1/direct-messages/api-features) where can you see a bunch of things you can do with Messages, We are specifically interested in Sending Messages, which are available at [Sending and receiving events](https://developer.twitter.com/en/docs/twitter-api/v1/direct-messages/sending-and-receiving/overview)

Step 4: On the API reference tab you can clearly see the Example Request you need to send, and the Example response you get back

Example Request:

```
curl --request POST
--url https://api.twitter.com/1.1/direct_messages/events/new.json
--header 'authorization: OAuth oauth_consumer_key="YOUR_CONSUMER_KEY", oauth_nonce="AUTO_GENERATED_NONCE", oauth_signature="AUTO_GENERATED_SIGNATURE", oauth_signature_method="HMAC-SHA1", oauth_timestamp="AUTO_GENERATED_TIMESTAMP", oauth_token="USERS_ACCESS_TOKEN", oauth_version="1.0"'
--header 'content-type: application/json'
--data '{"event": {"type": "message_create", "message_create": {"target": {"recipient_id": "RECIPIENT_USER_ID"}, "message_data": {"text": "Hello World!"}}}}'
```

Example Response

```
{
"event": {
  "type": "message_create",
  "message_create": {
    "target": {
      "recipient_id": "RECIPIENT_USER_ID"
    },
    "message_data": {
      "text": "Hello World!",
    }
  }
}
}
```

This basically means, you need to send a POST request to "https://api.twitter.com/1.1/direct_messages/events/new.json" with the Authorization header as

```
 'authorization: OAuth oauth_consumer_key="YOUR_CONSUMER_KEY", oauth_nonce="AUTO_GENERATED_NONCE", oauth_signature="AUTO_GENERATED_SIGNATURE", oauth_signature_method="HMAC-SHA1", oauth_timestamp="AUTO_GENERATED_TIMESTAMP", oauth_token="USERS_ACCESS_TOKEN", oauth_version="1.0"'
```

Step 5: If you are planning to pass the authorization headers manually You can generate the oauth parameters by following [this link](https://developer.twitter.com/en/docs/authentication/oauth-1-0a/authorizing-a-request) or you can use some third party libraries that handles all the behind the scenes, generation of this auth header such as [Twit](https://www.npmjs.com/package/twit)

We will step further using Twit

Step 6: Install Twit and generate all the required credentials such as Consumer Key (API key), Consumer Key Secret (API Secret), Access Token, Access Token Secret etc, by logging in into [Twitter Developer Dashboard](https://developer.twitter.com/en/portal/dashboard)

Step 7: Initialise Twit with all your credentials

```
var T = new Twit({
  consumer_key:         '...',
  consumer_secret:      '...',
  access_token:         '...',
  access_token_secret:  '...',
  timeout_ms:           60*1000,  // optional HTTP request timeout to apply to all requests.
  strictSSL:            true,     // optional - requires SSL certificates to be valid.
})
```

Step 8: Now you can use the "T" variable to make GET and POST calls

```
T.get("/your/api-endpoint", parameters, callback)
OR
T.post("/your/api-endpoint", parameters, callback)
```

The callback gives you three parameters: (error, data, response)

Step 9: For example to send a message to a recepient, following [Messages Docs](https://developer.twitter.com/en/docs/twitter-api/v1/direct-messages/sending-and-receiving/overview)

```
var params = {
    event: {
        type: "message_create",
        message_create: {
          target: { recipient_id: "YOUR_RECEPIENT_ID_HERE" },
          message_data: {
            text: `YOUR_MESSAGE_HERE`,
          },
        },
    },
};

T.post("direct_messages/events/new", params, function(err, data, response){
    if(!err){
        // MESSAGE IS SENT
    }
})
```

Please note that, the RECEPIENT_ID in the parameter is not the user Id, so to get the recepient id from the userID, you will need to make another call to twitter API, similarly, to fetch the recepeint id from the userID by performing a [User Loopkup](https://developer.twitter.com/en/docs/twitter-api/v1/accounts-and-users/follow-search-get-users/api-reference/get-users-lookup)

Also, you cannot send message to peoples who dont follow you. Similarly you can use different API endpoints available to fetch all your followers, and then send messages accordingly, or do any kind of fun stuff, with this API

## Summary

This was just an explanation to the problem, we discuused above, and tried to solve for the twitter. Similarly you can dig into other social media API's to get the same problem solved, for different social media platforms.

Hope this blogs help you to get an idea of the problem, and how to tackle such problems.
