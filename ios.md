---
layout: page
title: iOS SDK Integration
permalink: /ios/
additionalNavigation : [
    { "title" : "iOS SDK",                  "link" : "https://github.com/sensorberg-dev/ios-sdk" },
    { "title" : "iOS Bugtracker",           "link" : "https://github.com/sensorberg-dev/ios-sdk/issues" },
    { "title" : "Edit this page",           "link" : "https://github.com/sensorberg-dev/sensorberg-dev.github.io/edit/master/ios.md" }
]

---  

<div class="callout callout-alert">
    <h1><i class="fa fa-exclamation-triangle"></i>Note about the new portal:</h1>
    <p>Starting with version ({{ site.latestiOSRelease }}) of our iOS SDK, we switched to the new <a href="https://portal.sensorberg.com">Sensorberg Portal</a></p>
    <p>If you haven't migrated your account yet (you should!) please continue using the 2.4.1 version</p>
</div>

# Getting started with the Sensorberg SDK

*This is a guide to help developers get up to speed with Sensorberg iOS SDK. These step-by-step instructions are written for Xcode 7, using the iOS 8 SDK. If you are using a previous version of Xcode, you may want to update before starting.*  

## Demo app  

Clone the Repository from our [GitHub](https://github.com/sensorberg-dev/ios-sdk).  
Or runing `pod try SensorbergSDK` in a terminal will open the Sensorberg demo project.  
Select the `SBDemoApp` target and run on device.  

## Quickstart  

### 1. Create an account  

To get started with the Sensorberg SDK, [sign up for a free account](https://portal.sensorberg.com/)
*Read more about our [Beacon Management Platform](https://sensorberg.zendesk.com)*  

### 2. Cocoapods  

The easiest way to integrate the iOS SDK is via [CocoaPods](https://cocoapods.org/).
*If you're new to CocoaPods, visit their [getting started documentation](https://guides.cocoapods.org/using/getting-started.html).*


    $ cd your-project-directory
    $ pod init
    $ open -a Xcode Podfile


Once you've initialized CocoaPods, just add the [Sensorberg Pod](https://cocoapods.org/pods/SensorbergSDK) pod to your target:  

    target 'YourApp' do
    	pod "SensorbergSDK", "~> {{ site.latestiOSRelease }}"
    end

Now you can install the dependencies in your project:  


    $ pod install
    $ open <YourProjectName>.xcworkspace
  

### 3. Using the SDK   

Import the SensorbergSDK  

*ObjC :*  

    # import <SensorbergSDK/SensorbergSDK.h>
  

*Swift :*  

    import SensorbergSDK
  
Setup the SBManager with an **API key** and a **delegate**
*You can find your API key on the [Beacon Managerment Platform](https://portal.sensorberg.com) in the "Apps" section.
The Sensorberg SDK uses an [event bus](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) for events dispatching.
During setup, you pass the class instance that will receive the events as the `delegate`.*  

*ObjC :*  

    [[SBManager sharedManager] setApiKey:kAPIKey delegate:self];

*Swift :*  

    SBManager.sharedManager().setApiKey(kAPIKey, delegate: self)

Before starting the scanner, we need to ask permission to use the Location services.
If you want to receive events while the app is innactive, you need to pass `YES` to the `requestLocationAuthorization`. If you pass `NO`, the app will receive events only when active.  

*ObjC :*

    [SBManager sharedManager] requestLocationAuthorization:YES];

*Swift :*

    SBManager.sharedManager().requestLocationAuthorization(true)

> **Important**: Be sure to add the `NSLocationAlwaysUsageDescription` (or `NSLocationWhenInUseUsageDescription`) key to your plist file and the corresponding string to explain to the user why the app requires access to location.

Keep in mind that the SDK also requires the Bluetooth radio to be turned ON. You can check the status of the radio by calling:  

*ObjC :*

    [[SBManager sharedManager] bluetoothAuthorization] //returns SBBluetoothStatus
  
*Swift :*

    SBManager.sharedManager().bluetoothAuthorization() //returns SBBluetoothStatus
  
    The SDK also includes convenience methods to request the user to turn the Bluetooth radio on:  

*ObjC :*

    [[SBManager sharedManager] requestBluetoothAuthorization];

*Swift :*

    SBManager.sharedManager().requestBluetoothAuthorization()  

To be informed when there's a change in the Bluetooth radio status, SUBSCRIBE to SBEventBluetoothAuthorization:

*ObjC :*

    SUBSCRIBE(SBEventBluetoothAuthorization)
    {
        if (event.bluetoothAuthorization==SBBluetoothOn)
        {
            NSLog(@"Bluetooth ON, starting monitoring");
            [[SBManager sharedManager] startMonitoring];
        }
        else
        {
            NSLog(@"Bluetooth OFF, stopping monitoring");
            [[SBManager sharedManager] stopMonitoring];
        }
    }

*Swift :*

    func onSBEventBluetoothAuthorization(event:SBEventBluetoothAuthorization)
    {
        if (event.bluetoothAuthorization == SBBluetoothOn)
        {
    	    print("Bluetooth ON, starting monitoring")
            SBManager.sharedManager().startMonitoring()
        } 
        else
        {
    	    print("Bluetooth OFF, stopping monitoring")
            SBManager.sharedManager().stopMonitoring()
        }
    }
  

Once you setup the API key and the SDK starts monitoring, SUBSCRIBE to *SBEventPerformAction*:  

*ObjC :*

    SUBSCRIBE(SBEventPerformAction)
    {
    	UILocalNotification *notification = [[UILocalNotification alloc] init];
        notification.alertTitle = event.campaign.subject;
        notification.alertBody = event.campaign.body;
    	notification.alertAction = event.campaign.trigger == kSBTriggerEnter ? @"Enter" : @"Exit";
    	notification.alertAction = event.campaign.trigger == kSBTriggerEnterExit ? @"Enter&Exit" : notification.alertAction;
        if (event.campaign.fireDate)
        {
            notification.fireDate = event.campaign.fireDate;
        }
        else
        {
    	    notification.fireDate = [NSDate dateWithTimeIntervalSinceNow:1];
        }

        [[UIApplication sharedApplication] scheduleLocalNotification:notification];

    }
  
*Swift :*

    func onSBEventPerformAction(event:SBEventPerformAction)
    {
        let notification = UILocalNotification()
        notification.alertTitle = event.campaign.subject
        notification.alertBody = event.campaign.body
        notification.alertAction = event.campaign.trigger == kSBTriggerEnter ? "Enter" : "Exit"
        notification.alertAction = event.campaign.trigger == kSBTriggerEnterExit ? "Enter&Exit" : notification.alertAction
            
        if (event.campaign.fireDate != nil)
        {
            notification.fireDate = event.campaign.fireDate
        }
        else
        {
            notification.fireDate = NSDate.init(timeIntervalSinceNow: 1)
        }
            UIApplication.sharedApplication().scheduleLocalNotification(notification)
    }

Check out the [documentation](http://cocoadocs.org/docsets/SensorbergSDK/) for a list of all supported protocols.

To receive events in other class instances besides the **delegate**, the listener object  has to be registered on Event Bus like following example.  

*ObjC :*

    - (instancetype)init
    {
        if (self = [super init])
        {
            REGISTER();
        }
        
        return self;
    }

or

    - (void)viewDidLoad
    {
        [super viewDidLoad];
    	REGISTER();
    }
  

*Swift :*

    required init()
    {
        Tolo.sharedInstance().subscribe(self)
    }

or

    override func viewDidLoad()
    {
        super.viewDidLoad()
        Tolo.sharedInstance().subscribe(self)
    }  

## Documentation  
Documentation is available on [CocoaDocs](http://cocoadocs.org/docsets/SensorbergSDK).  

## Blog Posts 
- [What's new with iOS SDK!!](http://sensorberg-dev.github.io/2016/07/What's-NEW-in-iOS-SDK-2.1.2-!!/)   
- [Setting Custom API Keys, Resolver URLs, and Proximity UUIDs for Campaign Simulation](http://sensorberg-dev.github.io/2016/06/Custom-Resolver-URL-API-Key-and-Proximity-UUIDs/)
- [Conversion feature in iOS SDK](http://sensorberg-dev.github.io/2016/06/New-conversion-feature-in-iOS-SDK/)
-  [iOS Actionable Notification with Payload](http://sensorberg-dev.github.io/2016/06/iOS-Actionable-Notification-with-Payload/)    

## Sample Application on AppStore
- [**'Beacon Showcase'**](https://itunes.apple.com/us/app/beacon-showcase/id1115128115?mt=8) by Sensorberg GmbH.
- [How to use 'Beacon Showcase'](http://sensorberg-dev.github.io/2016/05/Beacon-Showcase-iOS-App-2-1/)


## Dependencies  

The **Sensorberg SDK 2.1.3 requires iOS 8.0** and uses:  

- [JSONModel](https://github.com/icanzilb/JSONModel) version 1.1 for JSON parsing
- [UICKeyChainStore](https://github.com/kishikawakatsumi/UICKeyChainStore) version 2.0 for keychain access
- [tolo](https://github.com/genzeb/tolo) version 1.0.1 for event communication
- [objc-geohash](https://github.com/lyokato/objc-geohash) version 0.0.2 for geocoding system  

## Support  

If you need any technical support, please file an issue at our [GitHub repository](https://github.com/sensorberg-dev/ios-sdk/issues/new) or contact [our support](https://sensorberg.zendesk.com/hc/en-us/requests/new).  

## Commercial Support  

Let us know how we can support you with developing awesome applications that include iBeacon functionality. Just [drop us](mailto:support@sensorberg.com) a message.  

## Bugs  

If you encounter any bugs, please [report them](https://github.com/sensorberg-dev/ios-sdk/issues).  

<!--<div class="callout callout-info">-->
<!--    <h1><i class='fa fa-info-circle'></i>Tip: Edit the default beacon regions</h1>-->
<!--    <p>By default, the SDK will monitor all <a href="https://sensorberg.zendesk.com/hc/en-us/articles/201635021-How-is-a-Beacon-ID-structured-">the Sensorberg beacon</a> regions and all the regions you specify at https://portal.sensorberg.com/. If you want to only use the actual regions of your active beacons set the default regions to an empty array:<br> -->
<!--    <pre><code class="language-text" data-lang="text">SBSDKManager.setDefaultRegions(@[])</code></pre>-->
<!--    Please note, you need the <a href="http://sensorberg-dev.github.io/ios-sdk/1.0.2/">1.0.2</a> release to use this feature</p>    -->
<!--</div>-->

<div class="callout callout-info">
    <h1><i class='fa fa-info-circle'></i>Dependency collisions?</h1>
    <p>If you have a dependency collision or you don´t want to integrate the sources of our SDK into your project, <a href="mailto:support@sensorbergcom">contact us</a>. We have <a href="https://github.com/sensorberg-dev/ios-sdk/tree/v2m">a script</a> which generates a "mangled" library, with obfuscated symbols.<strong>We only recommend to use it in very rare cases!</strong></p>
</div>
