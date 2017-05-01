# Sending push notifications with Urban Airship
Before digging into details it's important to understand the architectural idea
![image](https://cloud.githubusercontent.com/assets/1279756/25579133/383ca46e-2e75-11e7-8001-21d7e7d34f5a.png)

# Diagram
1) Through the UA SDK (or the native integration) the app will request a push-token at APNS/GCM/FCM/WNS. This will trigger a popup to allow push notification on most platforms. This push token is unique for the App, device & certification (release, debug etc) and will expire after x days. 

2) Through the SDK / API the push-token will be sent to UA and stored. UA can now send push messages to that device/app/certificate.

 The device also needs to register to channel(s), for easier segmentation
 - named user (Always register with your userId)
 - alias (deprecated)
 - tags (Push send to group of users, fx users who have push setting1 on, joins setting1 tag)

3) Sending a push notification from a backend project happens via the API.

4) UA will look up the namedUser or tag and loop through each push-tokens registered to this channel. And sent 1-by-1 to each push-network

5) The push-network will queue the request from UA and sent the push message through a socket connection to the device. If the device is not online, it will save it for x hours (depending on push-network)

# Apps

In UA you create applications for each application & environment.

For application ABC there should be 3 UA apps

- ABC - Development (development)
- ABC - Staging (production)
- ABC - Production (production)

The mode in () is the UA "Production status". The reason why we need "production" mode on staging is to have the option to do enterprise builds

The API keys can always be found at
https://go.urbanairship.com/apps/[APP-ID]/api/

If the certificates are not setup, this view cannot be found from navigation, so use the URL :)

The App Master Secret is only suppose to be used for sending push via the API. Never store this in apps / frontend

Note: All keys should be setup be server variables and never be hardcoded

# Alert
A push message has a "alert" which is the string showed in the notification center. It has a limit of 255 chars. So limit it to less. 

Note: iOS already truncate them earlier 

# Payloads

Payload is a way to pass more information, fx for deeplinking

Example of a payload could be

```json
{
 "type": "friendRequest",
 "userId": 1
}
```

This will let the app know where to deeplink

UA only not support nested data. If you need to send a full model, consider json-encoding it to 1 key
Note: there is limits on iOS for maximum 2kb of data

# Channels
There is currently 3 types of channels
 - named user (Always register with your userId)
 - alias (deprecated)
 - tags (Push send to group of users, fx users who have push setting1 on, joins setting1 tag)

We always register userId as namedUser (this used to be alias, but is now deprecated by UA)

Tags can be used very smart, and should avoid every situation of having to store push settings in the DB. 

Example 1)

Feature: Push on breaking news which is optional for users (is a setting)

The app will register to a tag "breakingNews" if the push setting is on.
It's important to code this as a way where local storage is master -> @johnny agree?

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

# Sound

There is options to change the notification sound, either to other native sounds or custom

- Setting sound to empty string "" will set no push sound
- Setting the sound to "default" will set the sound to platforms default tone

On top of that,
There is some differences between the different platforms

### iOS

Add a wav file in the build and add the sound key in the ios object via the API when sending a push message

ex
```
"notification": {
 "alert": "test",
 "ios": {
  "sound": "arrivedsound.wav"
 }
}
```
### Android
Android generally have more flexibility since they have the option to build the notification in a callback.

Often we just add it to the payload what sound file it should use
Sometimes the type field might be enought

### Windows

Right now it's only possible to use native sounds,
And can be set same as iOS, just add the sound key in the wns object

# Silent push notifications

It's possible to sent a push notification without it appears in the notification center. This can be very handy to trigger updates in the app, update badge counts etc. 

### iOS

This mode is called content-available for iOS
Inside the ios object, set the key "content-available": true
and make sure to not have badge, sound & alert set in the ios object

```json
{
 "notification": {
  "ios": {
   "content-available":true
  }
 }
}

```
### Android

Android is building the notification them self in runtime. That means just agree on some kind of indication on silent and 
Again the type could be enough, else create a payload key "silent": true fx

# Badge count
This is originally an iOS feature
![image](https://cloud.githubusercontent.com/assets/1279756/25580264/c21753e6-2e7f-11e7-9499-265f73ea79e9.png)

But there is an option for doing something simular on android also

![image](https://cloud.githubusercontent.com/assets/1279756/25580290/084cdfe8-2e80-11e7-9f09-e13c0c282866.png)

There is 2 ways of doing badge count

## The big solution

Like FB, Google etc, let the server keep track of unread count. And send silent push notification when this updates, to keep all you divices / platforms aligned 

This can be very time consuming and often require a full activity / notification system before hand

## The simple solution

Use the +1 value, which will increase the counter by one in ios. And when you open to the app / specific view, you clear the count

### iOS

Inside the ios object, set the key "badge": count or +1

```json
{
 "notification": {
  "ios": {
   "badge":"+1"
  }
 }
}
```

### Android

Android is building the notification them self in runtime. That means putting it in the payload should be just fine

### Windows

Not possible

# Localization 

First you need to setup a system to keep track of language for each user. 

Setup a field on the user model "locale" and store the Accept-Language header here.
Now when sending a push notification you can look up the user's locale before hand, and thereby translate it.

This solution requires you to loop each user. It is possible to send arrays of namedUsers/aliases to minimize the network to UA (which is the slow part)

Note: Also a creative solutions with tags can be used

# Other

- Android has a "delivery_priority" field which should be set to high if the notification is a must. Else low-battery modes etc will ignore it ("normal" or "high")
- Android has a Visibility option also. Which controls how much of the push message is showed in the lock screen (Android 5 feature)
