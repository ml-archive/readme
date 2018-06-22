# Sending push notifications with Firebase cloud message (FCM)
[WIP]

Before digging into details it's important to understand the architectural idea
![image](https://cloud.githubusercontent.com/assets/1279756/25579133/383ca46e-2e75-11e7-8001-21d7e7d34f5a.png)

# Diagram
1) Through the FCM-SDK (or the native integration) the app will request a push-token at it's platform push-network. This will trigger a popup to allow push notification on most platforms. This push-token is unique for the App, device & certification (release, debug etc) and will expire after x days. Luckily the UA SDK will deal with refreshing it.

2) Through FCM-SDK / FCM-API the push-token will be sent to FCM and stored. FCM can now send push notifications to that device & app & certificate. Since we do not want to deal with push-tokens vs users references. It's important to register to topic as well. [Read more](https://github.com/nodes-vapor/readme/blob/master/Documentation/how-to-urban-firebase-cloud-messaging.md#topics)

3) Sending a push notification from a backend project happens via the API. It's normally triggered by
 - Admin panel as a view, send to X, Y & Z with a custom alert.
 - Triggered by an event, fx some else liked your post. The alert message would be from NStack / Localization

4) FCM will look up the topic or topic conditions, loop through each push-tokens registered to this channel. And sent 1-by-1 to each push-network. This is done Async in queue.

5) The push-network will queue the request from FCM and sent the push notification through a socket connection to the device. If the device is not online, it will save it for x hours (depending on push-network)

# Apps

In FCM you create applications for each application & environment.

For application ABC there should be 3 FCM apps

- ABC - Development
- ABC - Staging
- ABC - Production

The API credential & url can always be found at
https://console.firebase.google.com/u/0/project/[app-name]/settings/serviceaccounts/adminsdk

Set up android / ios / web cloud message credentials here
https://console.firebase.google.com/u/0/project/[app-name]/settings/serviceaccounts/cloudmessaging

Note: Credentials (json) and database URI should be setup as server variables and never be hardcoded

# Alert
A push message has a "alert" which is the string showed in the notification center. It has a limit of 255 chars. So limit it to less. 

Always put the alert message in NStack / Localization

Note: iOS already truncate them earlier (around 110 chars) 

# Payloads / Extra

Payload is a way to pass more information, fx for deeplinking

Example of a payload could be

```json
"notification": {
 "ios": {
   "extra": {
    "type": "friendRequest",
    "userId": 1
   }
 }
}
```

This will let the app know where to deeplink

FCM does not support nested data. If you need to send a full model, consider json-encoding it to 1 key

Note: there is limits on iOS for maximum 2kb of data

# Topics

We always register userId as a topic `user_[ID]`eg `user_1`

Topics can be used very smart, and should avoid every situation of having to store push settings in the DB. 

Example 1)

Feature: Push on breaking news which is optional for users (is a setting)

The app will register to a tag "breakingNews" if the push setting is on.
It's important to code this as a way where local storage is master

The backend will now just push to the tag "breakingNews"

Example 2)

Feature: Recieving push notifications about athlete with id: 12345, after favoriting him/her

The app will register to tag "athelete_12345"

The backend will now just push to the tag "athlete\_[ID]" instead of looping users which have favorited it (maybe the state is even local)

Example 3)

The app have options to only receive news push notification during race or always 

The app will register to tag "newsAlways" or "newsDuringRace" or both

Backend will push to those tags, but will figure out if the the current time is durring race and add that tag also.

Note: Users who registered to both tags will not get the notification twice. 

# Advanced

## Sound



## Badge count
This is originally an iOS feature
![image](https://cloud.githubusercontent.com/assets/1279756/25580264/c21753e6-2e7f-11e7-9499-265f73ea79e9.png)

But there is an option for doing something simular on android also

![image](https://cloud.githubusercontent.com/assets/1279756/25580290/084cdfe8-2e80-11e7-9f09-e13c0c282866.png)

There is 2 ways of doing badge count

### The big solution

Like FB, Google etc, let the server keep track of unread count. And send silent push notification when this updates, to keep all you divices / platforms aligned 

This can be very time consuming and often require a full activity / notification system before hand

### The simple solution

Use the +1 value, which will increase the counter by one in ios. And when you open to the app / specific view, you clear the count

#### iOS

Inside the apns object, set the key "badge": count or +1

```json
{
  "payload":{
    "aps":{
      "alert":null,
      "sound":null,
      "content-available":0,
      "badge": 1,
      "header":{
        "apns-priority":10
      }
    }
  }
}

 ```

#### Android

Add it to payload. 
Note: this is not native Android!

```json
"notification": {
 "android": {
   "extra": {
      "badge":"+1"
   }
 }
}
```


# Localization 

First you need to setup a system to keep track of language for each user. 

Setup a field on the user model "locale" and store the Accept-Language header here.
Now when sending a push notification you can look up the user's locale before hand, and thereby translate it.

This solution requires you to loop each user. It is possible to send arrays of namedUsers/aliases to minimize the network to UA (which is the slow part)

Note: Also a creative solutions with tags can be used
