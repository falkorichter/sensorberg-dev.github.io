---
layout: post
title: "Delayed notification issue on Huawei devices"
date: 2016-12-21
comments: true
tags: beacon SDK Huawei SensorbergSDK
---


Over the last couple months we've noticed some issue with delayed notifications
on some Huawei devices.

In this article we will explain the issue, the cause and
some possible fixes.

# The issue

This is a known issue with Huawei devices and is being caused by the battery manager and the
way it's trying to save battery.


# Possible fixes
There are couple settings that can help.
<!--more-->

On Android 5 and lower:

- Change the power plan to "normal" under settings → power saving
- Add the app as protected App under settings → Protected apps

On Android 6 and higher:

- Change the power plan to "normal" under settings → general settings
- Add the app as protected App
- Change the optimization setting under settings → apps → advanced → ignore
  battery optimizations


  <img alt="Huawei power saving setting" src="/images/posts/2016-12-21-Huawei-delayed-notification-issue/power-saving.png" width="30%"/>     <img alt="Huawei protected app setting" src="/images/posts/2016-12-21-Huawei-delayed-notification-issue/protected-app.png" width="30%" />

# Test Application
To investigate this issue further we wrote an application to test delayed
notifications on Huawei devices and also on other devices for references. The project can be found
under [Github.](https://github.com/sensorberg-dev/alarmmanager-test)

  <img alt="Notification Test1" src="/images/posts/2016-12-21-Huawei-delayed-notification-issue/notification_test_app1.png" width="30%"/>     <img alt="Notification Test2" src="/images/posts/2016-12-21-Huawei-delayed-notification-issue/notification_test_app2.png" width="30%" />

# Relevant code
The relevant code for this test case and delayed notifications are the following.
{% highlight java %}
alarmManager.setExact(AlarmManager.RTC_WAKEUP,  System.currentTimeMillis() + 3 * 1000 , pendingIntent);
{% endhighlight %}

If you experience similar issues feel free to discuss in the comments.
