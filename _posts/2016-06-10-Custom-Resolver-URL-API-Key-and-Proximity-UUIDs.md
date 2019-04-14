---
layout: post
title: "Setting Custom API Keys, Resolver URLs and Proximity UUIDs for Campaign Simulation"
date: 2016-06-10
comments: true
tags: beacon showcase iOS
---
# Setting Custom API Keys, Resolver URLs and Proximity UUIDs for Campaign Simulation on the iOS Showcase App

The iOS Showcase app has the following 3 features.

1. Simulating a beacon with a custom proximity UUID
2. Changing the Resolver URL for the App
3. Setting the Application API Key manually on the App instead of scanning QR-Code

<!--more-->

### Simulating beacons with custom proximity UUIDs
  
In the Showcase app we have 'Beacon Simulation' functionality. Previously it was only possible to simulate beacons with predefined proximity UUIDs in the app.  With our latest release it is now posible to add a custom proximity UUID and simulate any beacon associated with it.
  
**Add custom proximity UUIDs in the 'Advanced options' section of your application**
  
1. Select your application.
2. Select Advanced option.
3. Enter a custom proximity UUID and associate a name to it.
4. Save the iOS Settings.
5. Save Application.

Ex : custom UUIDs

```javascript
{
  "customProximityUUIDs": {
    "124C5678-4444-1111-2222-134556728422": "SB-GUNNIH"
  }
}
```

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZIMk9KxAW4k" frameborder="0" allowfullscreen></iframe>


*To use the new proximity UUID, scan the API Key for this app on your phone. Click on the ID field in the beacon simulator view, the new proximity UUID's name should appear in the list of available IDs.*

<iframe width="375" height="667" src="https://www.youtube.com/embed/nqwsBmGphQw" frameborder="0" allowfullscreen></iframe>

**Change the Resolver URL on the App**

1. Open the App.
2. Switch to Status Screen.
3. Tap on 'Reachable' Item in 'Sensorberg Platform' section.
4. Enter your own Resolver URL in the textfield of the dialog.
5. Select 'Ok'.
  
<iframe width="375" height="667" src="https://www.youtube.com/embed/AAtOiItxLeg" frameborder="0" allowfullscreen></iframe>

**Set the API Key Manually on the App instead of scanning QR-Code**

1. Open the App.
2. Switch to the Status Screen.
3. Tap on 'Valid API Key' in the 'Sensorberg Platform' section.
4. Or tap on 'Scan QR Code For API Key' in the 'Application' section. When the scanner comes up, tap the 'Enter manually' button.
5. Enter or paste your 'API Key' in the textfield of the dialog box.
6. Select 'Ok'.
  
<iframe width="375" height="667" src="https://www.youtube.com/embed/Ej6XP8K_WsI" frameborder="0" allowfullscreen></iframe><iframe width="375" height="667" src="https://www.youtube.com/embed/ZyvCgHSvpO0" frameborder="0" allowfullscreen></iframe>
