---
layout: post
title: "Android SDK Dependency Releases"
date: 2015-03-12 12:00:00 +1
comments: true
tags: android java open-source
---

Over the last weeks, we have open sourced the dependencies of the upcoming Android SDK.

There is currently 3 projects that are available via jcenter:

<!--more-->

# Volley [ ![Download](https://api.bintray.com/packages/sensorberg/maven/volley/images/download.svg) ](https://bintray.com/sensorberg/maven/volley/_latestVersion)
URL: [bintray.com/sensorberg/maven/volley/view](https://bintray.com/sensorberg/maven/volley/view)

We had to repackage the original Volley in order to distribute it with our SDK. Unfortunately Google does not publish Volley and in order to avoid duplicate classes with your own Volley dependencies, all classes were moved to the base package *com.sensorberg.android.volley*

# networkstate [ ![Download](https://api.bintray.com/packages/sensorberg/maven/networkstate/images/download.svg) ](https://bintray.com/sensorberg/maven/networkstate/_latestVersion)
URL [bintray.com/sensorberg/maven/networkstate/view](https://bintray.com/sensorberg/maven/networkstate/view)

We set this class up as a separate module in order to reuse it. Since the Android gradle plugin does not bundle local modules into a library module, this project had to be open sourced and released to jcenter as well.

# OkVolley [ ![Download](https://api.bintray.com/packages/sensorberg/maven/okvolley/images/download.svg) ](https://bintray.com/sensorberg/maven/okvolley/_latestVersion)

URL: [bintray.com/sensorberg/maven/okvolley/view](https://bintray.com/sensorberg/maven/okvolley/view)