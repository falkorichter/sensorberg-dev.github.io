---
layout: post
title: "Tip: Remove Secret Codes Broadcastreceiver from your manifest"
date: 2015-06-26 16:20:00 +1
comments: true
tags: sdk
---
If you don´t want to ship the secret codes feature of the SDK, add this to your *application* part of your manifest.

{% highlight xml %}
<receiver
   android:name="com.sensorberg.sdk.SensorbergCodeReceiver"
   tools:node="remove"
   tools:selector="com.sensorberg.sdk" />
{% endhighlight %}

If you leave the receiver active you can also trigger it using adb:
{% highlight bash %}
#enable logging
adb shell am broadcast -W -a android.provider.Telephony.SECRET_CODE -d android_secret_code://73676723741
#disable logging
adb shell am broadcast -W -a android.provider.Telephony.SECRET_CODE -d android_secret_code://73676723740

{% endhighlight %}
<!--more-->

<div class="callout callout-info">
    <h1><i class='fa fa-info-circle'></i>Check your manifest with apktool</h1>
    <p>To make sure this worked, test your final APK with <a href="http://ibotpeaches.github.io/Apktool/">apktool</a>:</p>
{% highlight bash %}
apktool d -s -f <your-apk-file>
{% endhighlight %}
</div>

This tip works with **all versions** of the Android SDK.
