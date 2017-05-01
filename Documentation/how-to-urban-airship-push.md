# Sending push notifications with Urban Airship
Before digging into details it's important to understand the arcitectural idea
![image](https://cloud.githubusercontent.com/assets/1279756/25579133/383ca46e-2e75-11e7-8001-21d7e7d34f5a.png)

### Steps
1) Through the UA SDK (or the native integration) the app will request a push-token at APNS/GCM/FCM/WNS. This will trigger a popup to allow push notification on most platforms. This push token is unique for the App, device & certification (release, debug etc) and will expire after x days. 

2) Through the SDK / API the push-token will be sent to UA and stored. UA can now send push messages to that device/app/certificate.

 The device also needs to register to channel(s), for easier segmentation
 - named user (Always register with your userId)
 - alias (deprecated)
 - tags (Push send to group of users, fx users who have push setting1 on, joins setting1 tag)

3) Sending a push notification from a backend project happens via the API.

4) UA will look up the namedUser or tag and loop through each push-tokens registered to this channel. And sent 1-by-1 to each push-network

5) The push-network will queue the request from UA and sent the push message through a socket connection to the device. If the device is not online, it will save it for x hours (depending on push-network)

### Apps

In UA you create applications for each application & environment.

For application ABC there should be 3 UA apps

ABC - Development (development)
ABC - Staging (production)
ABC - Production (production)

The mode in () is the UA "Production status". The reason why we need "production" mode on staging is to have the option to do enterprise builds

The API keys can always be found at
https://go.urbanairship.com/apps/[APP-ID]/api/

If the certificates are not setup, this view cannot be found from navigation, so use the URL :)

The App Master Secret is only suppose to be used for sending push via the API. Never store this in apps / frontend

Note: All keys should be setup be server variables and never be hardcoded

### Alert
A push message has a "alert" which is the string showed in the notification center. It has a limit of 255 chars. So limit it to less. 

Note: iOS already truncate them earlier 

### Payloads



### Tags

### Sound

### Silent push notifications

### Badge count

### Priority 
