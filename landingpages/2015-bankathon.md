---
layout: page
title: Bankathon 2015
permalink: /2015bankathon/
comments: true

---

# Welcome to the BANKATHON 2015

We just released {{ site.latestAndroidSDKRelease }} of the Android SDK and you can be the first one outside of sensorberg to explore it´s feature.
 
We´ve prepared this page to answer some of the questions you might have, any other questions, jump right to [support](#support):

## How to get an Account? 

Go to <a href="https://portal.sensorberg.com/#/signup">portal.sensorberg.com/#/signup</a> and register for free now.

## How Do I simulate beacons?

### Use one of the beacons that we have sent you

You can use a screwdriver to open them. This is super useful, because you can take out the battery and turn the beacon on and off. This is neccesary, since the SDK only reacts on enter and exit events.

### Use our showcase app on iOS

Download the [Showcase App for iOS](https://itunes.apple.com/de/app/sensorberg-showcase/id882711177?mt=8) and start your beacon.

<a href="https://itunes.apple.com/de/app/sensorberg-showcase/id882711177?mt=8"><img src="/images/pages/2015bankathon/showcaseiOS.jpeg" alt="screenshot of showcase on iOS"></a>

### Use our showcase app on Android (only on Nexus 6/9 and Lollipop'ed Galaxy S5)

Download the [Showcase App for Android](https://play.google.com/store/apps/details?id=com.sensorberg.android.showcase) and start your beacon.

<img src="/images/pages/2015bankathon/showcaseAndroid2.png" alt="Showcase Android - Simulate Beacon">

You may also use the bleeding edge Hockey version of the showcase app:

<a href="https://rink.hockeyapp.net/apps/f774155858d12f4931ab32adaab643bf"><img alt="QR code pointing to https://rink.hockeyapp.net/apps/f774155858d12f4931ab32adaab643bf" src="https://chart.googleapis.com/chart?cht=qr&chl=https%3A%2F%2Frink.hockeyapp.net%2Fapps%2Ff774155858d12f4931ab32adaab643bf&chs=256x256"></a>

### Use your Mac with BTLE Mountain Lion and our sensorbeacon app.

Download it [here](https://rink.hockeyapp.net/apps/00f9bc2b440ef8028c4bb8bc5cbcf494)

<href="https://rink.hockeyapp.net/apps/00f9bc2b440ef8028c4bb8bc5cbcf494"><img src="/images/pages/2015bankathon/sensorbeacon.png" alt="screenshot of sensorbeacon"></a>

## I´m cool, I want to enable the developer mode in the Android Showcase app:

<img src="/images/pages/2015bankathon/showcaseAndroid.png" alt="Showcase Android - Developer mode">


## We are only iOS developers and detection in the background is not working properly :(

If you want to have background notifications on iOS, all the beacons on the hackathon need to have different proximity UUIDs. Your should definitely configure them once with the BeaCfg app. You can also use the Showcase apps to simulate beacons, but make sure, nobody is on the same "channel"

## How do I change the beacons settings?

You need our Android app [Sensorberg BeaCfg](https://play.google.com/store/apps/details?id=com.sensorberg.bconfig) and an Account. You can set the advertisement interval and the transmission power.

## iOS??

Follow our <a href="/ios">iOS instructions</a>

## Android??

Follow our <a href="/android">Android instructions</a>

## What is possible?

Check our [latest blog post](/2015/04/Unlimited-Use-Cases-With-Action-Payload/) on a sample, what is possible with our SDK and some sample code. The Sample is only for Android, as the payload feature is still in development for iOS. But you can use In-App links to achieve something similar on iOS.

##Statistics with the new SDK?

Unfortunatly we´re still in the middle of releasing statistics, that work with the new SDK. They will propably be deployed on Monday 26th of April. So ignore the cloud statistics until then.

<span id="support"/>
## Support

Write an email to support@sensorberg.com, or use the [contact form](https://sensorberg.zendesk.com/hc/en-us/requests/new). You may also <a class="twitter-mention-button" href="https://twitter.com/intent/tweet?screen_name=sensordev">tweet at us</a>. Or ask a question with disqus on the bottom of the page


<script>
window.twttr=(function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],t=window.twttr||{};if(d.getElementById(id))return;js=d.createElement(s);js.id=id;js.src="https://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);t._e=[];t.ready=function(f){t._e.push(f);};return t;}(document,"script","twitter-wjs"));
</script>

  
