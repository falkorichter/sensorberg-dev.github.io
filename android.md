---
layout: page
title: Android SDK Integration
permalink: /android/
additionalNavigation : [
    { "title" : "Source Code",              "link" : "https://github.com/sensorberg-dev/android-sdk" },
    { "title" : "Android SDK Samples",      "link" : "https://github.com/sensorberg-dev/android-sdk-samples" },
    { "title" : "Android SDK Bugtracker",   "link" : "https://github.com/sensorberg-dev/android-sdk/issues" },
    { "title" : "faulty link",              "link" : "https://not-exists.com" }
]
---

<div class="callout callout-info">
    <h1><i class='fa fa-info-circle'/></i>Gradle only</h1>
    <p>Gradle is the only supported build system by Google and we also only support integrations if you build with Gradle. Integrating with maven should be identical, but can provide no support.</p>
</div>

You need to have the jcenter artifactory in your list of repositories and declare the dependency to our bootstrapper library. The *Bootstrapper* references the SDK.

{% highlight groovy %}
repositories {
    jcenter()    
}

dependencies {
       compile ('com.sensorberg.sdk:android-sdk-bootstrapper:2.+')
}
{% endhighlight %}

Set your API key in your manifest and define your *BroadcastReceiver*:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <application>
        <meta-data
            android:name="com.sensorberg.sdk.ApiKey"
            android:value="a8ab23e7f2c4fbdd07d0e0e14835db037d2f62584b976aa0026a671c60e0707f" />
        <receiver android:name="com.myCompany.MyActionPresenter"
            android:process=".sensorberg"
            android:exported:"false" >
            <intent-filter>
                <action android:name="com.sensorberg.android.PRESENT_ACTION" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
{% endhighlight %}

<div class="callout callout-alert">
    <h1><i class='fa fa-exclamation-triangle'/></i>The BroadcastReceiver is running in another process</h1>
    <p>You should be aware, that the Sensorberg Android SDK is running in a separate process. The broadcast will be sent in the separate process as well. The intention of the *BroadcastReceiver* is to present the content of your *Action* when the app is in background.</p>
</div>


Enable the SDK in your [Application](http://developer.android.com/reference/android/app/Application.html) object and register foreground/background notifications:

It´s now time to implement the BroadcastReceiver:

{% highlight java %}
public class MyActionPresenter extends BroadcastReceiver {
       @Override
       public void onReceive(Context context, Intent intent) {
           Action action = intent.getExtras().getParcelable(Action.INTENT_KEY);
           switch (action.getType()){
               case MESSAGE_URI:
                   UriMessageAction uriMessageAction = (UriMessageAction) action;
                   showNotification(context, action.getUuid().hashCode(), uriMessageAction.getTitle(), uriMessageAction.getContent(), Uri.parse(uriMessageAction.getUri()));
                   break;
               case MESSAGE_WEBSITE:
                   VisitWebsiteAction visitWebsiteAction = (VisitWebsiteAction) action;
                   showNotification(context, action.getUuid().hashCode(), visitWebsiteAction.getSubject(), visitWebsiteAction.getBody(), visitWebsiteAction.getUri());
                   break;
               case MESSAGE_IN_APP:
                   InAppAction inAppAction = (InAppAction) action;
                   showNotification(context, action.getUuid().hashCode(), inAppAction.getSubject(), inAppAction.getBody(), inAppAction.getUri());
                   break;
           }     
       }   
       
       private void showNotification(Context context, int id, String title, String content, Uri uri) {
           Intent sendIntent = new Intent();
           sendIntent.setAction(Intent.ACTION_SEND);
           sendIntent.putExtra(Intent.EXTRA_TEXT, title + "\n" + content + "\n" + uri.toString());
           sendIntent.setType("text/plain");
   
           Notification notification = new NotificationCompat.Builder(context)
                   .setContentIntent(PendingIntent.getActivity(
                           context,
                           0,
                           new Intent(Intent.ACTION_VIEW, uri),
                           PendingIntent.FLAG_UPDATE_CURRENT))
                   .setContentTitle(title)
                   .setContentText(content)
                   .setSmallIcon(R.drawable.ic_launcher)
                   .setAutoCancel(true)
                   .setShowWhen(true)
                   .build();
           NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
           notificationManager.notify(id, notification);
       }
{% endhighlight %}

This class receives a broadcast, if the SDK has detected a beacon and successfully resolved an associated Action. We´re using the Support Library, be aware that this will require an additional dependency:

{% highlight groovy %}
compile "com.android.support:support-v4:22.0.0"
{% endhighlight %}

If you want your own UI to react to detected beacons and actions, please extend the *Bootstrapper* and add your customisations:


{% highlight java %}
public class MyBootstrapper extends SensorbergApplicationBootstrapper {
  public void presentBeaconEvent(BeaconEvent beaconEvent) {
      //your custom code
    }
}
{% endhighlight %}
<div class="callout callout-alert">
    <h1><i class='fa fa-exclamation-triangle'/></i>Process</h1>
    <p>The Bootstrapper runs in your own process, so you are free to access any singletons or statics that you use in your Application. Push the BeaconEvent to your EventBus, Otto and react as you wish.</p>
</div>

<br/>
<br/>
<br/>
<br/>
