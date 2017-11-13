---
layout: post
title: "New conversion feature in iOS SDK"
date: 2016-06-07
comments: true
tags: beacon iOS tracking conversion SDK
---

# Tracking User Actions with the Sensorberg Conversion Feature.

The "Conversion" feature enables you to measure user interactions with beacons and campaigns.
With this feature you can track the following informations:
- how many users were around the beacon region
- how many campaigns were delivered to the app
- how many campaign actions were performed by users

<!--more-->

**New "action" property in  SBMCampaignAction**

This "action" property is a unique id to report conversion information.

{% highlight objc %}

@interface SBMCampaignAction : NSObject
...
// action : unique action fire event identifier
@property (strong, nonatomic) NSString      *action;
@end

{% endhighlight %}

When you receive an "SBEventPerformAction" event from the SDK, it will contain an 'SBMCampaignAction' object (assuming it doesn't have an error :D).
Now you can use new 'action' parameter to report this conversion through 'SBManager'

**Currently we have following 4 conversion types.**
{% highlight objc %}
/**
 SBConversionType
 Represents the conversion type for a specific campaign action
 @since 2.1.2
 */
typedef enum : NSUInteger {
    /**
     *  The campaign action can't "fire" (ex. the user has denied access to local notifications)
     */
    kSBConversionUnavailable = -2,
    /**
     *  The campaign was suppressed by the app
     */
    kSBConversionSuppressed = -1,
    /**
     *  The campaign action has been "fired" but was ignored by the user
     *
     * @discussion: The campaigns are marked as "ignored" by default. To correctly measure conversion, be sure to call [SBManager sharedManager] reportConversion: forCampaignAction:] when performing the campaign action
     */
    kSBConversionIgnored = 0,
    /**
     *  The campaign action has been performed successfully
     */
    kSBConversionSuccessful = 1
} SBConversionType;

{% endhighlight %}

**Example for kSBConversionSuccessful :  When user has interacted with the notification**

{% highlight objc %}

- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
{
    if (notification.userInfo)
    {
        NSDictionary *dict = [notification.userInfo valueForKey:@"action"];
        SBMCampaignAction *action = [SBUtilities campaignActionFromDictionary:dict];
        if (action)
        {
            [[SBManager sharedManager] reportConversion:kSBConversionSuccessful forCampaignAction:[action.action copy]];
        }
    }
}

{% endhighlight %}

**Example for kSBConversionUnavailable : when the user cannot be notified.**

{% highlight objc %}

SUBSCRIBE(SBEventPerformAction)
{
    if (![[SBManager sharedManager] canReceiveNotifications])
    {
        [[SBManager sharedManager] reportConversion:kSBConversionUnavailable forCampaignAction:[action.action copy]];
    }
    else
    {
        // schedule notification
        [[SBManager sharedManager] reportConversion:kSBConversionIgnored forCampaignAction:[action.action copy]]
    }
}

{% endhighlight %}

In this case you can also show an alert and let your customer decide whether to perform an action or not.

Default Conversion value for campaign action is "**ignored**" (kSBConversionIgnored) - we overwrite conversion value when you report conversion through

{% highlight objc %}
[[SBManager sharedManager] reportConversion:SBConversionType forCampaignAction:action];
{% endhighlight %}

[Corresponding blogpost about conversions for Android](/2017/11/Streamlined-conversion-feature-on-Android-SDK-2-4/ "Corresponding blogpost about conversions for Android")

Enjoy the Conversions !!

Sensorberg Tech
