---
layout: post
title: "Sensorberg Android SDK is ready for Android 6 Permissions, are you?"
date: 2016-07-06
comments: true
tags: beacon sdk android
---
# How Android will handle permissions in Android 6 will affect the Sensorberg SDK.

Google is changing how Android will handle permissions. Unfortunately, these changes
will affect the Sensorberg SDK. In this article we will first explain the new Android permission framework, 
we will then discuss how this affects the Sensorberg SDK, finally we will explain how you can update 
your app to work with Android 6 permissions. 

The major difference between permissions in previous versions of Android and 
Android 6 is quite simple. In Android 6 there is the addition of Run-time permissions.
What does this exactly mean? Well, if you remember in previous Android versions when the 
user downloaded your applications they would be asked if they accept the permissions 
you have defined in your Android Manifest. This remains. The additional 
requirement now is that at Run-Time, not just at install, the user will be asked if 
they want or still want to grant the permission you have defined. Though all permissions
are not created equally. 

Google as decided to categories permissions into "dangerous" and "normal" permissions.
Dangerous permissions are simply those permissions which (could) put the user's security/data
at risk. And this is why our SDK is affected. 
This doesn't appear to be intuitive at first, we will get to that shortly.

Ok. Here is the secret. Your app will continue to scan for beacons in Android 6, but
here is the kicker, if you want it to scan in the background, which for a beacon is precisely
what we want, then you need to update your permissions in Android 6.
Bluetooth LE though is not actually a dangerous permission. So in theory you should not have to ask for a permission.
Bluetooth LE, in theory can give away your location, so Google has decided to make it mandatory for
when using BLE, to require one of the two location permissions, (fine or coarse grain) so users are 
can made aware of the issues surrounding Bluetooth LE.
 
So, we now don't actually have to worry about Bluetooth, the previous permissions still stand. 
Now we have to ask for one of two location permissions for beacon scanning to work properly. Before we discuss how to do that,
we should first explain how we now support location services in our SDK. We basically receive an intent which flags
the scanner to stop or start based on whether location permissions are set. If your app does targets Android 6, 
the SDK will send the developer a log message that they need to ask the user for their permission 
to use Location Services. The sdk will not crash the app, simply no beacons will be found. 
Yes, it is true that in the foreground even without the location permission beacons may be found,
though we decided to just disable scanning to avoid problems and confusion. Wow, that was a lot,
but we have one more topic to discuss, implementation from your side.
  
As an user of our SDK you need to do one thing; you need to at some point before scanning is to occur to ask
your users for the Fine Location Services.  So how to do this.. see the example below from the developer test app.
  
  
```java
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
```
 
 Finally we need to receive the callback from the ```requestPermissions()``` call. 
 
 
```java
     @Override
     public void onRequestPermissionsResult(int requestCode,
                                            String permissions[], int[] grantResults) {
         switch (requestCode) {
             case MY_PERMISSION_REQUEST_LOCATION_SERVICES: {
                 if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                     Log.d("Scanner Message", "location permission granted");
                     ((DemoApplication) getApplication()).setLocationPermissionGranted(SensorbergServiceMessage.MSG_LOCATION_SET);
                 } else {
                     ((MyApplicati) getApplication()).setLocationPermissionGranted(SensorbergServiceMessage.MSG_LOCATION_NOT_SET_WHEN_NEEDED);
                 }
                 return;
             }
         }
     }
```

You will notice two things. There is a method we call from the application class and we have two constants.
These two constants are only flags to tell the method in the application class whether the location
permission requested was granted. The method can be called whatever you want, but what is key is you need
to use our method ```sendLocationFlagToReceiver``` in the ```SensorbergSdk``` class in your application class.

```java
 public void setLocationPermissionGranted(int type) {
        boot.sendLocationFlagToReceiver(type);
    }
```

Type parameter is whether the permission is set or not. The call will be received by the ```PermissionBroadcastReceiver``` class and processed.

And that is it. Not so difficult. Please be nice to your users and only ask for the permissions when you
will be scanning.  Don't over ask, also try to explain to your users why you need the permission,
some people will not understand why they need the location permission. Be friendly, descriptive and helpful.

If you have any questions, don't hesitate to reach out.

-The Sensorberg Android Team
