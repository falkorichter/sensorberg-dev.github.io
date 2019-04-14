---
layout: post
title: "Streamlined conversion feature on Android SDK 2.4"
date: 2017-11-10
comments: true
tags: beacon Android tracking conversion SDK
---

# Streamlined conversion feature on Android SDK 2.4

The latest Sensorberg Android SDK streamlines the conversion integrations.
The main idea of this new API is that the SDK will not assume any state changes
and gives the app developers the freedom to define the specific moment
each conversion state should be changed.

This change also makes the Android SDK more in line with the behavior of the iOS SDK,
that way unifying their presentation on the dashboard on portal.sensorberg.com.

<!--more-->

## The new conversion API

The following static method have been added to SensorbergSdk class.

{% highlight java %}

/**
 * * Call this to let SDK know the Action conversion status changed
 *
 * @param context            the callers context
 * @param actionInstanceUuid instance Uuid of the {@link com.sensorberg.sdk.action.Action} to update the status
 * @param conversion         the new conversion status
 */
public static void notifyConversionStatus(Context context,
                                          String actionInstanceUuid,
                                          Conversion conversion);

{% endhighlight %}

The host app is responsible for calling this method on the appropriate moments.
The previous `ActionReceiver` have been deprecated and removed from the SDK.

And that's all!

`Context` is available from `BroadcastReceiver` and `Activity`,
the UUID comes from `Action.getInstanceUuid()` and `Conversion` is a simple `enum` with 4 states:

* `NOTIFICATION_DISABLED` - The host app wanted to show a notification, but the user disabled them.
* `ACTION_SUPPRESSED` - The host app received the `Action` but decided not to act on it. This is useful case the action intention is to simply trigger some background sync on the app.
* `NOTIFICATION_SHOWN` - The host app received the `Action` and shown a notification to the user.
* `SUCCESS` - The user tapped on the notification. This status is usually updated from inside the host app activity.

## Typical implementation


Add to the app BroadcastReceiver calls to `notifyConversionStatus` and add `Action` meta-data to the notification intent:
{% highlight java %}
public class MyActionReceiver extends BroadcastReceiver {
  @Override
  public void onReceive(Context context, Intent intent) {

    Action action = intent.getExtras().getParcelable(Action.INTENT_KEY);

    if (NotificationManagerCompat.from(context).areNotificationsEnabled()) {

      // Parse the Action and show notification
      ... create notification

      // add conversion meta-data to the intent
      intent.putExtra("conversion", action.getInstanceUuid());

      ... create PendingIntent and show notification

      // Notify the SDK
      SensorbergSdk.notifyConversionStatus(context, action.getInstanceUuid(), Conversion.NOTIFICATION_SHOWN);
  } else {
      SensorbergSdk.notifyConversionStatus(context, action.getInstanceUuid(), Conversion.NOTIFICATION_DISABLED);
  }
}
{% endhighlight %}

Handle the action meta-data in the activity code:
{% highlight java %}
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
      ... normal activity initialization

      handleActionConversion(getIntent());
  }

  @Override
  protected void onNewIntent(Intent intent) {
      super.onNewIntent(intent);
      handleActionConversion(intent);
  }

  private void handleActionConversion(Intent intent) {
    String conversion = getIntent().getStringExtra("conversion");
    if (conversion != null) {
        SensorbergSdk.notifyConversionStatus(this, conversion, Conversion.SUCCESS);
    }    
  }
{% endhighlight %}


[Corresponding blogpost about conversions for iOS](https://developer.sensorberg.com/2016/06/New-conversion-feature-in-iOS-SDK/ "Corresponding blogpost about conversions for iOS")

Enjoy simple Conversions!
