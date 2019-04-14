---
layout: post
title: "How do I... implement the Advertiser Identifier in Android?"
date: 2016-07-25
comments: true
tags: beacon sdk android
---
For the Advertiser Identifier there are multiple ways of implementing the Google identifier depending on your application. We
will show an example of what can be done. Of course the most important thing is to set and unset the ID, how 
you do that is where the implementation can differ.


- Set the identifier. Please note, you will most likely want input from the user, like a switch or button to toggle the setting of the value. 
In the example below we used a switch, below is the switch listener. 
{% highlight java %}
    @OnCheckedChanged(R.id.use_google_advertiser_id_switch)
    void googleAdvertiserIdSwitchChanged(boolean checked) {
        if (!this.resumed) {
            return;
        }
        if (checked) {
            Logger.log.logSettingsUpdateState(ShowcaseTracking.ADVERTISER_IDENTIFIER_ENABLED);
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        AdvertisingIdClient.Info info = AdvertisingIdClient.getAdvertisingIdInfo(getActivity().getApplicationContext());
                        ShowcaseApplication.getInstance().boot.setAdvertisingIdentifier(info.getId());
                    } catch (IOException e) {
                        Logger.log.logError("foreground could not fetch the advertising identifier because of an IO Exception", e);
                    } catch (GooglePlayServicesNotAvailableException e) {
                        Logger.log.logError("foreground play services not available", e);
                    } catch (GooglePlayServicesRepairableException e) {
                        Logger.log.logError("foreground  services need repairing", e);
                    } catch (Exception e) {
                        Logger.log.logError("foreground could not fetch the advertising identifier because of an unknown error", e);
                    }
                }
            }).start();
            Toast.makeText(getActivity(), ShowcaseTracking.ADVERTISER_IDENTIFIER_ENABLED, Toast.LENGTH_LONG).show();
        } else {
            Logger.log.logSettingsUpdateState(ShowcaseTracking.ADVERTISER_IDENTIFIER_DISABLED);
            ShowcaseApplication.getInstance().boot.setAdvertisingIdentifier(null); //set to null ie. turn off.
            Toast.makeText(getActivity(), ShowcaseTracking.ADVERTISER_IDENTIFIER_DISABLED, Toast.LENGTH_LONG).show();
                    }
    }
{% endhighlight %}

- If you need to unset, you can set the identifier to null. 

{% highlight java %}
ShowcaseApplication.getInstance().boot.setAdvertisingIdentifier(null);
{% endhighlight %}

The user can reset the Google identifier through their device settings.

-The Sensorberg Android Team
