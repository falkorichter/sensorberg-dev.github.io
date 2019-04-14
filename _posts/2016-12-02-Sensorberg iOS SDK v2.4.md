---
layout: post
title: "Sensorberg iOS SDK v2.4"
date: 2016-12-02
comments: true
tags: beacon SDK iOS SensorbergSDK radar
---
  
This week we launched [version 2.4 of our iOS SDK](https://github.com/sensorberg-dev/ios-sdk) - be sure to update!  

# Improvements  

- Added support for iOS 10
- Added support for individual beacons monitoring
- Added support target attributes

# Fixed Bugs  

- Fixed geohash missing from some analytics
- Fixed warnings related to deprecated methods usage

The biggest change in this version though is related to actual beacon monitoring, which changed with iOS 10. 
Click through to read more about this change.  

<!--more-->

### Monitoring Beacon Regions in iOS (10)

Beacon monitoring uses the Bluetooth radio on your iOS device to detect nearby beacons. You need to create a `CLBeaconRegion` and tell the `CLLocationManager` to "monitor" for that region.  
You can create a `CLBeaconRegion` via 3 different initializers:  

1. `initWithProximityUUID:identifier:`  
This allows your app to monitor for *all* the beacons which have a specific proximity UUID. This seems like the best option and it's what most iBeacon SDKs and apps do.  

2. `initWithProximityUUID:major:identifier:`  
Very similar to the previous initializer, this allows you to monitor for all iBeacons with a specific Proximity UUID and a major.

3. `initWithProximityUUID:major:minor:identifier:`  
This creates a `CLBeaconRegion` for a specific beacon, by specifying the Proximity UUID, major and minor.  

Prior to iOS 10, after creating a `CLBeaconRegion`, the SDK (and subsequently the app) would be notified anytime your iDevice would enter or exit a beacon region. Once we got that information from the system, we would start "ranging" - basically asking the system to "listen" to the beacon.  
In iOS 10 Apple decided to change the behaviour of the monitoring process and "ranging" beacons while the app is in background no longer works.  

```
Question: Wouldn't ranging beacons in background consume more battery?  
Answer: Actually no. The OS is already receiving the advertisement packets from the beacon, so forwarding them to the app doesn't actually increase battery usage.
```

In [version 2.4](https://github.com/sensorberg-dev/ios-sdk) we switched to monitoring for specific beacons, by creating the `CLBeaconRegion` using the Proximity UUID, major and minor. This continues to work as expected, though it does have a big caveat: **you can only monitor a maximum of 20 regions**.

We filed a radar (bug report) with Apple and we hope that a future iOS version will actually fix this behaviour.  
On the same note, be sure to update your iOS device to the latest version (currently 10.1.1) as 10.0 and 10.1 have some [other bluetooth issues](https://goo.gl/nJyEgr) :)

Greetings from Berlin!

# Related Posts  

- [Conversion measurement[Beta WIP].](http://sensorberg-dev.github.io/2016/06/New-conversion-feature-in-iOS-SDK/)  
- Remote configurations for Campaign  
	- [Actionable Notification with Campaign Payload](http://sensorberg-dev.github.io/2016/06/iOS-Actionable-Notification-with-Payload/)  
	- [Custom Proximity UUIDs for Campaign Simulation](http://sensorberg-dev.github.io/2016/06/Custom-Resolver-URL-API-Key-and-Proximity-UUIDs/)  

