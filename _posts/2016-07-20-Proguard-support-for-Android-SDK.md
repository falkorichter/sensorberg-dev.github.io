---
layout: post
title: "Proguard support for Android SDK"
date: 2016-07-20
comments: true
tags: beacon SDK Android SensorbergSDK Proguard
---
  
We have added support for Proguard to our Android SDK, from release 1.2.1.

It has been added on a library level, so the only thing you need to do in your project is to add/set `minifyEnabled true` in your build.gradle file. We have tested it extensively in our internal builds and our Showcase app, but if you find any problems, don't hesitate to reach out or open an issue on Github.

-The Sensorberg Android Team
