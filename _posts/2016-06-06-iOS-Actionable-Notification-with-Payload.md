---
layout: post
title: "iOS Actionable Notification with Payload"
date: 2016-06-06
comments: true
tags: beacon showcase iOS
---
# Customise Your notification with Payload

Since 2015 April, we have had a payload feature[[see here](http://sensorberg-dev.github.io/2015/04/Unlimited-Use-Cases-With-Action-Payload)] in the [Sensorberg Management Platform](https://manage.sensorberg.com/#/applications).
In this post we will highlight the usage of the payload object for [actionable notifications on iOS devices](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIMutableUserNotificationAction_class/) and show different usages of the payload object.

## Use Case:
We would like to show an actionable notification with custom content to our users based on the payload which we have entered user into the [management platform](https://manage.sensorberg.com/#/applications).  Using this article you can see how to set an actionable notification with a custom payload and how to handle it.

<!--more-->
**Add content in payload of your campaign**

<iframe width="560" height="315" src="https://www.youtube.com/embed/36ZR_Metkrw" frameborder="0" allowfullscreen></iframe>
1. Select a campaign first.
2. Select the Advanced option.
3. Enter your custom payload contents
4. Save the Campaign.

"onEnter" is the object which contains the content for one actionable notification.

- "onEnter" object will contain contents for any actions which have triggerType "On Enter"
- "onExit" object will contain contents for any actions which have triggerType "On Exit"

"confirmationAction" can have one of following 2 options : *open*, *share*

- open	: open the URL which is given in campaign.
- share : share the URL, which is given in campaign, on Facebook or elsewhere.

"actInForeground" is a boolean value

- if it's true, the app will come to the foreground and do its required actions when the user taps the 'ok' button or taps directly on the notification. Otherwise the app will do the specified "confirmationAction" in the background.

The following 3 language objects ("de", "en", "ko") are localised strings for notification. Of course you can add more languages with [ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) language codes.

- "okButtonTitle" : ok button title on notification.
- "cancelButtonTitle" : cancel button title on notification.
- "title": subject on notification
-  "message" : message on notification

Ex : custom payload

{% highlight javascript %}
{
  "onEnter": {
    "confirmAction": "open",
    "actInForeground": true,
    "de": {
      "okButtonTitle": "Zur Website",
      "cancelButtonTitle": "Schließen",
      "title": "Hallo!!",
      "message": "Wilkommen in Sensorberg!"
    },
    "en": {
      "okButtonTitle": "Open Website",
      "cancelButtonTitle": "Close",
      "title": "Hi!!",
      "message": "Welcome to Sensorberg!"
    },
    "ko": {
      "okButtonTitle": "사이트 열기",
      "cancelButtonTitle": "닫기",
      "title": "안녕하세요!!",
      "message": "Sensorberg에 오신것을 환영합니다!"
    }
  }
}
{% endhighlight %}


**Get content of payload from SBCampaignAction**

To get SBMCampaignAction, you need to subscribe "SBEventPerformAction" with Tolo.

- <code> SUBSCRIBE(SBEventPerformAction){ SBMCampaignAction *action = event.campaign; } </code>

And check for a payload in the SBMCampaignAction object

- <code> if (action.payload) { ... } </code>

Get the "onEnter" content from the payload

- <code> NSDictionary *actionDict =  [action.payload @"onEnter"] </code>

Register UserNotificationSettings.


{% highlight objc %}
NSDictionary *localizedDict = [actionDict objectForKey:@"en"];
UIMutableUserNotificationAction *okAction = [UIMutableUserNotificationAction new];
okAction.identifier = @"okActionIdentifier";
okAction.title = [localizedDict objectForKey:@"okButtonTitle"];
okAction.authenticationRequired = NO;
okAction.activationMode = [[actionDict objectForKey:@"actInForeground"] boolValue] ?UIUserNotificationActivationModeForeground : UIUserNotificationActivationModeBackground;

UIMutableUserNotificationAction *cancelAction = [UIMutableUserNotificationAction new];
cancelAction.identifier = @"cancelButtonIdentifer";
cancelAction.title = [localizedDict objectForKey:@"cancelButtonTitle"];
cancelAction.authenticationRequired = NO;
// cancel action should not activate app.
cancelAction.activationMode = UIUserNotificationActivationModeBackground;
UIMutableUserNotificationCategory *actionCategory;
actionCategory = [[UIMutableUserNotificationCategory alloc] init];
[actionCategory setIdentifier:@"myOnEnterActionCategoryIdeintifier"];
[actionCategory setActions:@[okAction, cancelAction] forContext:UIUserNotificationActionContextDefault];

NSSet *categories = [NSSet setWithObject:actionCategory];
UIUserNotificationType types = (UIUserNotificationTypeAlert|
                                UIUserNotificationTypeSound|
                                UIUserNotificationTypeBadge);

UIUserNotificationSettings *settings;
settings = [UIUserNotificationSettings settingsForTypes:types categories:categories];

[[UIApplication sharedApplication] registerUserNotificationSettings:settings];

{% endhighlight %}


Create a local notification with a "notification category" and then schedule the  notification.


{% highlight objc %}

UILocalNotification *n = [UILocalNotification new];
n.alertTitle = [localizedDict objectForKey:@"title"];
n.alertBody = [localizedDict objectForKey:@"message"];
n.category =  @"myOnEnterActionCategoryIdeintifier";
n.userInfo =@{@"action": @"User Info what you need to handle"};
if (action.fireDate)
{
    n.fireDate = action.fireDate;
}
else
{
    n.fireDate = [NSDate dateWithTimeIntervalSinceNow:1];
}

[[UIApplication sharedApplication] scheduleLocalNotification:n];

{% endhighlight %}


# The Result

Let's see how it works on iPhone :D

<iframe width="375" height="667" src="https://www.youtube.com/embed/2xqAm41u08E" frameborder="0" allowfullscreen></iframe><iframe width="375" height="667" src="https://www.youtube.com/embed/9lDainFUTZs" frameborder="0" allowfullscreen></iframe>
