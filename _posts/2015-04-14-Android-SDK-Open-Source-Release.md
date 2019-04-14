---
layout: post
title: "Open sourcing the Android SDK"
date: 2015-04-14 17:02:00 +1
comments: true
tags: android java open-source
---
We have decided to Open Source the Sensorberg Android SDK. You will find the source at [github.com/sensorberg-dev/android-sdk](http://github.com/sensorberg-dev/android-sdk). Feel free to investigate its structure and don't forget pull-requests on issues that you encounter.

The SDK is a Release Candidate for the upcoming 1.0 release. It should be fully functional except some backend integrations for statistic features. We will keep you informed about the formal release of the 1.0 Version of the SDK.

<!--more-->

What´s new:

 * jcenter hosting of the artifacts
 * source code release of the complete SDK
 * offline capabilities based on standard HTTP caching
 * pre-emptive caching, sync your beacon layout so that you resolve beacons when you´re offline later
 * improved integration, easier api-key setup, easier setup of foreground and background notifications
 * full access to notifications api, implement your own interactive notifications!

 and many more changes under the hood.

 We will continue to post about details of the SDK with code samples. We have been testing all these features extensively with an improved version of our [Showcase application](https://play.google.com/store/apps/details?id=com.sensorberg.android.showcase).