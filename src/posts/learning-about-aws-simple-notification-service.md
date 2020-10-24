---
layout: layouts/post.njk
title: Learning about AWS Simple Notification Service
date: 2020-10-24T05:54:31.157Z
tags:
  - AWS
  - jam stack
  - serverless
---
Quick life update, back in June I joined a company called FightCamp! I'm working a full stack role with all the goodness of the Jam Stack. Which means I need to learn AWS.

The first project I worked on with AWS was to give a customer a subscription credit on Stripe when they check out with a promo code. Since our whole backend system is Serverless this means I needed to learn about AWS Lambdas, SNS, and Stripe. Let's do it!

## Simple Notification Service (SNS)
This is what took me a little extra effort to understand. And honestly, the AWS documentation did not help. Turns out SNS is a Pub-Sub service. 

**What is Pub-Sub?** It's a [pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) where there is a publisher and a subscriber. This means one service sends out a message that another service is listening to. They are independent of each other, the publisher doesn't need to know who will receive the message, its job is just to send. The subscriber doesn't know anything about the publisher, and can even subscribe to multiple messages.

In our case, we have a message published when a customer checks out, this is called a Topic in SNS. This Topic, let's call it "Purchase", is published through SNS and we have multiple different services subscribed to it. One of those is an AWS Lambda that will take the message's payload, parse some information from it, and then call the Stripe API to give a credit. 



## Using Serverless Application Framework

The other thing I needed to learn was how to use the [Serverless Framework](https://www.serverless.com/). We use it because it makes it easier to deploy to AWS and in my opinion it also kind of documents your project. You can open up a `serverless.yml` file and figure out what the lambda is for. I found the [SNS documentation](https://www.serverless.com/framework/docs/providers/aws/events/sns/#sns/) for Serverless super helpful. You define a function in the `functions` section, give it a handler (your Lambda), and in `events` you point it to your `sns` topic. 

```yaml
functions:
  mySubscriber:
    handler: index.myHandlerLambda
    events:
      - sns: arn:aws:sns:us-east-....
```
To make an SNS topic, you go to the AWS console, search for SNS in their handy search bar, and click on 'Create topic' button. After you create the topic, copy the `arn` and paste that into the `serverless.yml`. 

## Lambda and Stripe
The third part of this recipe is the Lambda that's going to hit Stripe and give the credit. In your `index.js` file you will export a function that takes a parameter, we call it event, that will have your data. 
``` 
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);

module.exports.myHandlerLambda = async (event) => {
    const message = event.Records[0].Sns.Message;
    const data = JSON.parse(message);
   // ...do stuff here to get the customer data you need...
    stripe.customers.createBalanceTransaction(id, credit);
}
```
