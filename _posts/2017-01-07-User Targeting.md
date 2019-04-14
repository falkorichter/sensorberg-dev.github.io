---
layout: post
title: "User targeting with the Sensorberg SDK"
date: 2017-01-07
comments: true
tags: sdk targeting
---
  
With the release of the [iOS SDK 2.4](https://github.com/sensorberg-dev/ios-sdk/releases/tag/2.4) and [Android SDK 2.2](https://github.com/sensorberg-dev/android-sdk/releases/tag/v2.2.0-RAILS) we have also released the capability to target a specific subgroup of your app users. 

The system is very flexible and can be used for multiple use cases. Here is a list of examples:

* Send a notification to all logged in users of my app
* Send a notification to all users of my app who have a score above 1000
* Send a notification to all users who have specified their hometown to be Berlin

From a data privacy perspective, we designed the feature in a way so that data is only stored on the client. We are providing a new API in the SDKs the enable customers to set any ```String key values``` which filters the campaigns for the current app user.
<!--more-->
Here an example which can be used in your application:

{% highlight java %}  
public class MyApplication extends MultiDexApplication {
	public SensorbergSdk sensorbergSDK;
	@Override
    public void onCreate() {
    	//initialize your SDK properly
    	Map<String, String> attributes = new HashMap<>();
    	if (userMananger.userloggedIn) {
    		attributes.put("user_LoggedIn", "true");
    		attributes.put("user_hometown", userMananger.getHomeTown());
    	}
    	attributes.put("score", "above1000");
    	SensorbergSDK.setAttributes(attributes);
    }
}  
{% endhighlight %}

And on iOS:
{% highlight ObjC %}  
NSDictionary *attributes = @{ @"userLoggedIn":@YES,  
                              @"userHomeTown":@"Berlin"};  
[[SBManager sharedManager] setTargetAttributes:attributes];  
{% endhighlight %}
To clear the attributes pass a `nil`: `[[SBManager sharedManager] setTargetAttributes:nil]`  


## Notes:

 * You are responsible for keeping the attributes in sync. If the user logs out, make sure to clear the attributes and send them to the SDK again.
 * If you don´t clear the attributes, they remain unchanged in the SDK.
 
<div class="callout callout-info">
    <h1><i class="fa fa-info-circle"></i> Note: This feature is only available on the new portal</h1>
    <p>Your account must be on <a href="https://portal.sensorberg.com">portal.sensorberg.com</a> if you want to use this feature. A full release of the management UI on this feature is scheduled for January 2017.</p>
    <p>You should already have your app ready to use this feature, so start integrating today!</p>
</div>
