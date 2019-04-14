---
layout: post
title: "READ_SYNC_SETTINGS permission optional"
date: 2015-06-26 14:00:00 +1
comments: true
tags: sdk
---
If, for some reason your don´t like the READ_SYNC_SETTINGS permission to be added to your application since our Android SDK is using it, just add this line to your manifest:

{% highlight xml %}
<uses-permission
            android:name="android.permission.READ_SYNC_SETTINGS"
            tools:node="remove"
            tools:selector="com.sensorberg.sdk" />
{% endhighlight %}

This change comes to affect in the final Android 1.0.1 release.

Read all about the tools:remove feature in the <a href="http://tools.android.com/tech-docs/new-build-system/user-guide/manifest-merger#TOC-tools:node-markers">Manifest Merger documentation</a>


<div class="callout callout-alert">
    <h1><i class='fa fa-exclamation-triangle'></i>Don´t remove before 1.0.1</h1>
    <p>Don´t follow this tip unless you´re using 1.0.1. Previous versions of the SDK do not know about this optional permission and will crash.</p>
</div>

<!--more-->

<div class="callout callout-info">
    <h1><i class='fa fa-info-circle'></i>Check your manifest with apktool</h1>
    <p>To make sure this worked, test your final APK with <a href="http://ibotpeaches.github.io/Apktool/">apktool</a>:</p>
{% highlight bash %}
apktool d -s -f <your-apk-file>
{% endhighlight %}
</div>