---
layout: post
title: "The Good, the Bad and the Ugly. Exit strategy since 2.3.3-RAILS"
date: 2017-03-29
comments: true
tags: beacon Android exit strategy timeout
---
# Some facts

- Android is notoriously bad with its Bluetooth stack.
- New BT API introduced in Lollipop makes it somewhat better (however brings new set of problems).
- Sensorberg **SDK v2.x** uses old BT API only.
- The **SDK v3** using new BT API is on its way!

# The Bad

**Before SDK 2.3.3-RAILS** exit time was defined as **absolute time** since the last seen beacon, minus _only last_ scan pause time.  The exit condition was checked every second of the scan time. This solution is not inherently bad, but combined with using old API and somewhat crooked implementation, it had problems:

- Exit could be triggered after 1 second of scan, followed immediately by enter.
- Subtracting only _last_ pause time limited it to 2 scan cycles max.
- In case of Android BT error (infamous status=133) false exits were triggered.
- In general, exits were quickly triggered, but very inconsistently.

# The Ugly (but stable!)

**Since SDK 2.3.3-RAILS** exit time is defined as a **sum of scan times** we haven't seen given beacon. There is also grace period from the beginning of every scan when exit can't be triggered, even though the exit time has passed. This solution results in:

- More control over exit behavior.
- Elasticity to mimic the old exit strategy through using Settings (link here).
- Grace time protecting from exiting right at beginning of the scan.
- Less false exits in case of Android BT error occuring.
- In general, exits are slower but way more consistent and configurable, so better suited for real world cases.
<br><br>

<p>Let's consider behavior of SDK 2.3.3-RAILS on its default settings, while app is in background:<br>
{% highlight json %}
{
	"scanner.exitTimeoutMillis": 40000,
	"scanner.backgroundScanTime": 15000,
	"scanner.backgroundWaitTime": 120000,
	"scanner.exitBackgroundGraceMillis": 7500
}
{% endhighlight %}
<p>Translated to graph this is:</p>
<img alt="Legend" src="/images/posts/2017-03-29-The-Good-the-Bad-and-the-Ugly/legend.png" width="100%"/>
<p>Let's assume the beacon was last seen 3s after the first scan cycle started:</p>
<img alt="Example exit strategy" src="/images/posts/2017-03-29-The-Good-the-Bad-and-the-Ugly/example.png" width="100%"/>
<p>Time needed for exit is 40s, so we need to finish the first cycle (12s), run second cycle (15s) and then exit is triggered in third cycle (after 13s of it). If the exit should appear in first 7.5s of the scan (grace time) it will be delayed till the end of the grace time.</p>
<div class="callout callout-alert">
<p>In case of Android optimizing AlarmManager alerts the 120s wait time above may easily become 300s, thus prolonging exit times even further (up to 15 mins). The default settings are very defensive, resulting in stable exits even for worse phones, but may not be what you want. If your case needs quicker exits (but less consistency on older phones) experiment with lower exit timeout, e.g:</p>
{% highlight json %}
{
	"scanner.exitTimeoutMillis": 20000
}
{% endhighlight %}
</div>

# The Good

Proper solution is using new BT API combined with burst scanning (splitting scan time into sub-scans), plus grace time and possibly some hard-limited time of exit defined as absolute time since we've seen beacon.

**And it's coming soon**!
