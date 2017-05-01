# Sending push notifications with Urban Airship
Before digging into details it's important to understand the arcitectural idea
![image](https://cloud.githubusercontent.com/assets/1279756/25579133/383ca46e-2e75-11e7-8001-21d7e7d34f5a.png)

### Steps
1) Through the UA SDK (or the native integration) the app will request a push-token at APNS/GCM/FCM/WNS. This will trigger a popup to allow push notification on most platforms. This push token is unique for the App, device & certification (release, debug etc) and will expire after x days. 

2) Through the SDK / API the push-token will be sent to UA and stored. UA can now send push messages to that device/app/certificate.

But before we stop just here, we want this app to register to channel(s)
 - named user (Always register with your userId)
 - alias (deprecated)
 - tags (Push send to group of users, fx users who have push setting1 on, joins setting1 tag)

3) Sending a push notification from a backend project happens via the API.

4) UA will look up the namedUser or tag and loop through each push-tokens registered to this channel. And sent 1-by-1 to each push-network

5) The push-network
### Apps

### Tags

### Payloads

### Sound

### Silent push notifications

