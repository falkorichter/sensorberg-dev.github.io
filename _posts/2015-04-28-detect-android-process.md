---
layout: post
title: "Detect your process"
date: 2015-04-28 12:00:00 +1
comments: true
tags: sdk
---
# Detect the process your **Application** objects are running in.

When integrating the Sensorberg SDK you probably did notice, that we run in a separate process. This also means that our SDK process has its *own* Application instance.

The "Sensorberg Application object" actually should not do of your logic. No need to instantiate singletons...

IF you want to detect the process you´re running in here is some code:

<!--more-->


{% highlight java %}
public class DemoApplication extends Application
{

    @Override
	public void onCreate() {
		super.onCreate();

        String currentProcName = getProcessName();
        if (currentProcName != null && currentProcName.endsWith(":sensorberg")){
            Log.d(TAG, "we´re the Service process, so we´re ending here");
            return;
        }
        //you´re in the right process, init the Sensorberg SDK, do whatever you like to do
	}

    private String getProcessName(){
        BufferedReader cmdlineReader = null;
        try {
            cmdlineReader = new BufferedReader(new InputStreamReader(
                    new FileInputStream(
                            "/proc/" + android.os.Process.myPid() + "/cmdline"),
                    "iso-8859-1"));
            int c;
            StringBuilder processName = new StringBuilder();
            while ((c = cmdlineReader.read()) > 0) {
                processName.append((char) c);
            }
            return processName.toString();
        } catch (FileNotFoundException e) {

        } catch (UnsupportedEncodingException e) {

        } catch (IOException e) {

        } finally {
            if (cmdlineReader != null) {
                try {
                    cmdlineReader.close();
                } catch (IOException e) {
                }
            }
        }
        return null;
    }
}
{% endhighlight %}

There is another method, but it feels wrong to iterate over all running processes. Also [it´s been reported](http://stackoverflow.com/questions/19631894/is-there-a-way-to-get-current-process-name-in-android) that this list might not yet contain your own process.

{% highlight java %}
private String getProcessName(){
    int pid = android.os.Process.myPid();
    ActivityManager manager = (ActivityManager) this.getSystemService(Context.ACTIVITY_SERVICE);
    for (ActivityManager.RunningAppProcessInfo processInfo : manager.getRunningAppProcesses())
    {
        if (processInfo.pid == pid)
        {
            return processInfo.processName;
        }
    }
    return null
{% endhighlight %}