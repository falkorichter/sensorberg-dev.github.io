---
layout: post
title: "iOS SDK callback cleanUp 1.0.5"
date: 2015-09-10 01:00:00 +1
comments: true
tags: ios sdk
---

We have cleaned up the callbacks in the iOS SDK in the [1.0.5 release](https://github.com/sensorberg-dev/ios-sdk/releases/tag/1.0.5). We are now exposing the [SBSDKBeaconAction](https://github.com/sensorberg-dev/ios-sdk/blob/v1/SensorbergSDK/SBSDKBeaconAction.h) directly which contains all the necessary field you will need for your integration.

The change will also imply, that the integration needs to take care of the application state. If your app is in the background, show an ```UILocalNotification```, when you app is open, you can choose to show custom UI. This sample shows an easy UIAlertView:

The short version:

1. Your delegate receives the action in ```beaconManager:didResolveAction```
  1. when the app is in the foreground, we show our in-app UI immediately.
  2. when the app is in the background, we can only show a ```UILocalNotification```
  3. when the action has a delay, schedule a notification
2. ```application:didReceiveLocalNotification``` receives the local notification and the attached data when the app is opened. Get the metadata off the notification and show the same custom UI.

Please check the latest sample implementation in the [SBSDKAppDelegate.m](https://github.com/sensorberg-dev/ios-sdk/blob/v1/Example/Demo/SBSDKAppDelegate.m) on github, here are the relevant methods:

{% highlight objc %}
# pragma mark - Local Notifications & actions

- (void)beaconManager:(SBSDKManager *)manager didResolveAction:(SBSDKBeaconAction *)action {
    if ([UIApplication sharedApplication].applicationState != UIApplicationStateActive || action.delaySeconds.integerValue > 0){
        [self displayLocalNotificationForAction:action];
    } else {
        [self showActionAsAlertView:action];
    }
}

- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
    NSLog(@"%s %@", __PRETTY_FUNCTION__, notification.alertBody);
    if (notification.userInfo[SBSDKAppDelegateLocalNotificationActionKey]) {
        SBSDKBeaconAction * action = [NSKeyedUnarchiver unarchiveObjectWithData:notification.userInfo[SBSDKAppDelegateLocalNotificationActionKey]];
        [self showActionAsAlertView:action];
    }
}

- (void)displayLocalNotificationForAction:(SBSDKBeaconAction *)action {
    // Construct local notification.
    UILocalNotification *localNotification = [[UILocalNotification alloc] init];

    localNotification.alertBody = [NSString stringWithFormat:@"%@\n%@", action.subject, action.body];
    localNotification.alertAction = @"Open";
    localNotification.soundName = UILocalNotificationDefaultSoundName;
    localNotification.userInfo = @{ SBSDKAppDelegateLocalNotificationActionKey   : [NSKeyedArchiver archivedDataWithRootObject:action]};
    if (action.delaySeconds.integerValue > 0) {
        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:action.delaySeconds.doubleValue];
        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
    } else {
        [[UIApplication sharedApplication] presentLocalNotificationNow:localNotification];
    }

    self.localNotifications[action.actionId] = localNotification;
}

- (void)showActionAsAlertView:(SBSDKBeaconAction *)action {
    //show a boring notification:
    NSDictionary * payload = action.payload;
    //do something usefull with the payload, we´e boring and will just show an UIAlertView

    NSString * body;
    if (payload){
            body = [NSString stringWithFormat:@"%@\nPayload:\n%@", action.body, [action.payload description]];
        } else {
            body = action.body;
        }

    UIAlertView * alertView = [[UIAlertView alloc] initWithTitle:action.subject
                                                             message:body
                                                            delegate:self
                                                   cancelButtonTitle:@"Ignore"
                                                   otherButtonTitles:@"Open URL", nil];
    objc_setAssociatedObject(alertView, SBSDKAppDelegateInAppMessageUrlKey, action.url, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    objc_setAssociatedObject(alertView, SBSDKAppDelegateInAppPayloadKey, payload, OBJC_ASSOCIATION_RETAIN_NONATOMIC);

    [alertView show];
}

# pragma mark UIAlertViewDelegate

- (void)alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex {
    // Check for associated URL to be presented to the user.
    NSURL *url = objc_getAssociatedObject(alertView, SBSDKAppDelegateInAppMessageUrlKey);
    NSDictionary * payload = objc_getAssociatedObject(alertView, SBSDKAppDelegateInAppPayloadKey);
    //do something usfull with the payload. We´e boring and we´l just open the URL

    if ((alertView.firstOtherButtonIndex == buttonIndex) && (url != nil)) {
        [[UIApplication sharedApplication] openURL:url];
    }
}

{% endhighlight  %}
