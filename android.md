---
layout: page
title: Android SDK Integration
permalink: /android/
additionalNavigation : [
    { "title" : "Source Code",              "link" : "https://github.com/sensorberg-dev/android-sdk" },
    { "title" : "Android SDK Samples",      "link" : "https://github.com/sensorberg-dev/android-sdk-samples" },
    { "title" : "Android SDK Bootstrapper", "link" : "https://github.com/sensorberg-dev/android-sdk-bootstrapper" },
    { "title" : "Android SDK Bugtracker",   "link" : "https://github.com/sensorberg-dev/android-sdk/issues" },
    { "title" : "JCenter files",            "link" : "http://jcenter.bintray.com/com/sensorberg/sdk/android-sdk/" },
    { "title" : "Edit this page",           "link" : "https://github.com/sensorberg-dev/sensorberg-dev.github.io/edit/master/android.md" }
]
---

<div class="callout callout-alert">
<h2>SDK migration to new RAILS platform</h2>
<p>Starting with SDK 2.2.0-RAILS we're using newly implemented Sensorberg IoT RAILS platform as a backend.
This system upgrade will ensure better functionality, project use cases and&nbsp;simpler user experience.</p>
<p>As a result, a new account must be set up on the Sensorberg IoT platform in order to continue your projects.
This applies to every dependency change from <b>non-RAILS</b> version to <b>-RAILS</b> based version.</p>
<p>Simply create an account by visiting <a href="http://portal.sensorberg.com" target="_blank">portal.sensorberg.com</a>. You have the option to manually move your data (e.g., beacons, campaigns etc) to the new platform and continue to manage your account.&nbsp;</p>
<p>Alternatively, you may contact our support team at <a href="mailto:support@sensorberg.com">support@sensorberg.com</a>&nbsp;for assistance in the migration procedure.</p>
<p>For more useful knowledge articles, please visit our&nbsp;<a href="https://support.sensorberg.com/hc/en-us/categories/115000135909-Sensorberg-IoT-Enterprise-Platform" target="_blank">Knowledge Center.</a></p>
<p>The latest current artifact is:</p>
{% highlight groovy %}
compile 'com.sensorberg.sdk:{{ site.latestAndroidSDKRelease }}'
{% endhighlight %}

If you are using version 2.2.0 still, you are <strong>required to change the base URL</strong> in your Application class in a static initializer:
{% highlight java %}
static {		
   com.sensorberg.sdk.internal.transport.RetrofitApiTransport.RESOLVER_BASE_URL = "https://portal.sensorberg-cdn.com";	
} 
{% endhighlight %}
</div>

# How to install the Sensorberg Android SDK

You will need to have the jcenter artifactory in your list of repositories and declare the dependency to our <a href="http://jcenter.bintray.com/com/sensorberg/sdk/android-sdk/{{site.latestAndroidSDKRelease}}/">sdk</a>.

{% highlight groovy %}
repositories {
    jcenter()
}

dependencies {
       compile 'com.sensorberg.sdk:android-sdk:{{ site.latestAndroidSDKRelease }}'
}
{% endhighlight %}

Declare your <em>BroadcastReceiver</em>:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <application>
        <receiver android:name="com.myCompany.MyActionPresenter"
            android:process=".sensorberg"
            android:exported="false">
            <intent-filter>
                <action android:name="com.sensorberg.android.PRESENT_ACTION" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
{% endhighlight %}
<em>You cannot add a BroadCastReceiver at runtime! We are using a <a href="http://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html">LocalBroadcastManager</a> to send the broadcast and find the receiver(s).</em>

<div class="callout callout-alert">
    <h1><i class="fa fa-exclamation-triangle"></i>The BroadcastReceiver is running in another process</h1>
    <p>You should be aware, that the Sensorberg Android SDK is running in a separate process. The broadcast will be sent in the separate process as well. The intention of the <em>BroadcastReceiver</em> is to present the content of your <em>Action</em> when the app is in background.</p>
</div>

Enable the SDK in your [Application](http://developer.android.com/reference/android/app/Application.html) object and register foreground/background notifications:

# Integration
{% highlight java %}
public class DemoApplication extends Application {
    private SensorbergSdk sdk;
    private BackgroundDetector detector;

    static {
    	if (BuildConfig.DEBUG){
    		Logger.enableVerboseLogging();
    	}
    }

    @Override
	public void onCreate() {
		super.onCreate();

        sdk = new SensorbergSdk(this, API_KEY);//the context object and your api key.
        sdk.registerEventListener(new SensorbergSdkEventListener() {
            @Override
            public void presentBeaconEvent(BeaconEvent beaconEvent) { //your presentBeaconEvent action.
                showAlert(beaconEvent.getAction(), beaconEvent.trigger);
                Log.i("beaconevent", beaconEvent.getBeaconId().toString());
                Action action = beaconEvent.getAction();
                showAlert(action, beaconEvent.trigger);
            }
        });

        detector = new BackgroundDetector(sdk);
        registerActivityLifecycleCallbacks(detector);
	}
}
{% endhighlight %}

It´s now time to implement the <em>BroadcastReceiver</em>:

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

This class receives a broadcast, if the SDK has detected a beacon and successfully resolved an associated Action.

## Advertiser ID

There're several ways of acquiring Advertiser ID which varies per platform. Here we'll show for the Google ID.

{% highlight java %}
// running on a background thread due to networking
new Thread(new Runnable() {
    @Override
    public void run() {
        try {
            AdvertisingIdClient.Info info = AdvertisingIdClient.getAdvertisingIdInfo(context);
            // boot is the instance of ShowcaseBootstrapper instantiated during Application.onCreate()
            MyApp.getInstance().bootStrapper.setAdvertisingIdentifier(info.getId());
        } catch (IOException e) {
            Log.e(TAG, "Could not fetch advertising id", e);
        } catch (GooglePlayServicesNotAvailableException e) {
            Log.e(TAG, "Could not fetch advertising id, Google Play Service not available", e);
        } catch (GooglePlayServicesRepairableException e) {
            Log.e(TAG, "Could not fetch advertising id, Google Play Service need repairing", e);
        } catch (Exception e) {
            Log.e(TAG, "Could not fetch advertising id", e);
        }
    }
}).start();
{% endhighlight %}

And of course, if you need to remove it, just call it null

{% highlight java %}
MyApp.getInstance().bootStrapper.setAdvertisingIdentifier(null);
{% endhighlight %}

<div class="callout callout-alert">
    <h1><i class="fa fa-exclamation-triangle"></i> Android 6 Permissions</h1>
    <p>If you app will target android 6 you will need to prompt the user for location permissions before scanning will work - this should be down in the activity. For
       a more in-depth discussion please see <a href="https://developer.sensorberg.com/2016/07/Be-Ready-For-Android6-Permissions">the Android 6 blog</a>.</p>


In your activity which would use the scanner you need to ask for (location permission) at runtime:

{% highlight java %}
        if (ContextCompat.checkSelfPermission(this,
                Manifest.permission.ACCESS_FINE_LOCATION)
                != PackageManager.PERMISSION_GRANTED) {

            if (ActivityCompat.shouldShowRequestPermissionRationale(this,
                    Manifest.permission.ACCESS_FINE_LOCATION)) {
                final AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.setTitle("Functionality limited");
                builder.setMessage("Since location access has not been granted, " +
                        "this app will not be able to discover beacons when in the background.");
                builder.setPositiveButton(android.R.string.ok, null);
                builder.setOnDismissListener(new DialogInterface.OnDismissListener() {

                    @Override
                    public void onDismiss(DialogInterface dialog) {
                        ActivityCompat.requestPermissions(DemoActivity.this,
                                new String[]{Manifest.permission.ACCESS_FINE_LOCATION},
                                MY_PERMISSION_REQUEST_LOCATION_SERVICES);
                    }

                });
                builder.show();
            } else {
                ActivityCompat.requestPermissions(this,
                        new String[]{Manifest.permission.ACCESS_FINE_LOCATION},
                        MY_PERMISSION_REQUEST_LOCATION_SERVICES);
            }
        }

{% endhighlight %}

Then you must receive the callback.

{% highlight java %}
     @Override
     public void onRequestPermissionsResult(int requestCode,
                                            String permissions[], int[] grantResults) {
         switch (requestCode) {
             case MY_PERMISSION_REQUEST_LOCATION_SERVICES: {
                 if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                     Log.d("Scanner Message", "coarse location permission granted");
                     ((DemoApplication) getApplication()).setLocationPermissionGranted(SensorbergServiceMessage.MSG_LOCATION_SET);
                 } else {
                     ((DemoApplication) getApplication()).setLocationPermissionGranted(SensorbergServiceMessage.MSG_LOCATION_NOT_SET_WHEN_NEEDED);
                 }
                 return;
             }
         }
     }
{% endhighlight %}

<p>Please note that if you're using geofencing <a href="https://github.com/sensorberg-dev/android-sdk/tree/geofencing">dev preview branch</a> the permission ACCESS_FINE_LOCATION is required in your manifest. This is caused by Google Play Services requirements under the hood.</p><p>If ACCESS_FINE_LOCATION is not given you won't receive geofence notifications, but you can still receive those generated by beacons, provided your app holds ACCESS_COARSE_LOCATION permission</p>

</div>

### Development Tips

<div class="callout callout-info">
    <h1><i class="fa fa-info-circle"></i> Tip: Pretty ADB log with Android Bluetooth messages hidden</h1>
    <p>Use <a href="https://github.com/JakeWharton/pidcat">pidcat</a> with grep to show your log and hide the System Bluetooth scan logs:</p>
    {% highlight bash %}
    pidcat com.myapp.packageIdentifier | grep --invert-match BluetoothLeScanner
    {% endhighlight %}
</div>

<div class="callout callout-info">
    <h1><i class="fa fa-info-circle"></i> Tip: Create Your own account when developing</h1>
    <p>As as developer, you can create an account for free at <a href="https://manage.sensorberg.com/#/signup">manage.sensorberg.com/#/signup</a></p>
</div>
<div class="callout callout-info">
    <h1><i class="fa fa-info-circle"></i> Tip: Use the secret codes broadcastreceiver to add more debugging to your app.</h1>
    <p>Read all about it in this <a href="/2015/06/Tip-howto-remove-secred-codes-receiver/">blog post</a>.</p>
</div>
<div class="callout callout-info">
    <h1><i class="fa fa-info-circle"></i> Tip: Use conversions to measure user interaction.</h1>
    <p>Read more about conversions in respective <a href="/2017/02/Conversion-feature-for-Android-SDK/">Android</a> and <a href="/2016/06/New-conversion-feature-in-iOS-SDK/">iOS</a> blog posts.</p>
</div>
