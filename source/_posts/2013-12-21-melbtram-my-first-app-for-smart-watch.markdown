---
layout: post
title: "MelbTram - my first app for smart watch"
date: 2013-12-21 22:21:44 +1100
comments: true
categories: android smartwatch
---

I've recently bought
[Sony Smart Watch 2](http://www.amazon.com/gp/product/B00FB2XNCE/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00FB2XNCE&linkCode=as2&tag=gregdziesblog-20)
with the intention of developing apps for it ([check out MelbTram](https://play.google.com/store/apps/details?id=pl.agilesurfing.melbtram)).

I am quite happy with this purchase, it's really convenient to be able to see who is calling or read a message at simple glance.
I have chosen Sony Smart Watch, mainly because it runs Android and supports my [Nexus phone](http://www.amazon.com/gp/product/B00ABPKHH0/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00ABPKHH0&linkCode=as2&tag=gregdziesblog-20).

I have created my first app in two sessions on consecutive weekends (two Sundays). The first session was about being able to deploy
simple "Hello World" app to the watch.

As I never developed before for Android, the first challenge was how to put my phone into debug mode. It's quite easy:

{% blockquote mr.boyfox http://stackoverflow.com/questions/16707137/how-to-find-and-turn-on-usb-debugging-mode-on-nexus-4 %}
At the “About” screen, scroll to the bottom and tap on “Build number” seven times.
{% endblockquote %}

If your device is in debug mode, you should be able to run, and see:
{% codeblock %}
$> ./platform-tools/adb devices
List of devices attached
XXXXXXXXXXXXXXXX	device
{% endcodeblock %}

Then, I was following instructions from [Sony Developer website](http://developer.sonymobile.com/knowledge-base/tutorials/android_tutorial/how-to-create-an-app-extension-for-sony-smartwatch-2/)
and I was able successfully deploy my "Hello World". Yay!

The second sessions was about actually developing the app. The app is truly [a minimum viable product](http://www.amazon.com/gp/product/0307887898/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0307887898&linkCode=as2&tag=gregdziesblog-20), created and deployed to Play store within one day ;)
The problem I've decided to tackle was "when is the best time to leave home/office in order to catch a tram?".

I've based my solution on "SampleControlExtension" provided with the SDK.
Using it as starting point, step by step I've transformed it into the final form.
The app is very simple. It consists of "Preferences" screen on the phone, where you setup your tram stops and routes,
and simple screen on the watch displaying next 2 tram arrival times.

The app let's you configure 4 different routes. When you open the app on the watch, 4 background tasks like this
will be started:

{% codeblock lang:java %}
import com.sonyericsson.extras.liveware.extension.util.control.ControlExtension;

import android.os.AsyncTask;
import android.util.Log;

public class RetriveTramInfoTask  extends AsyncTask<String, Void, TramArrivals> {

    private Exception exception;
    private SampleControlSmartWatch2 myListener;


    private TramInfoRequest request;
    int viewLabelId = -1;

    public RetriveTramInfoTask(SampleControlSmartWatch2 myListener, int viewLabelId, TramInfoRequest request) {
    	this.myListener = myListener;
    	this.viewLabelId = viewLabelId;
    	this.request = request;
    }

    protected TramArrivals doInBackground(String... urls) {
        try {
        	TramTrackerAPI api = new TramTrackerAPI();
        	return api.nextTram(request);
        } catch (Exception e) {
        	e.printStackTrace();
            this.exception = e;
            return new TramArrivals(null);
        }
    }

    protected void onPostExecute(TramArrivals result) {
        String message = String.format("%d in %s minutes, then %s", request.routeId, result.get(0), result.get(1));
    	myListener.setWatchEvent(viewLabelId, message);
    }
}
{% endcodeblock %}

The end result looks more or less like this:

{% img https://lh6.ggpht.com/mQNZMEdtsCHhPVWCl5QmDSe6k5sE-a3U5e-dV64IASoWYSLcnV3wMxW603qYm8iXUQ=h310-rw %}

[The MelbTram app](https://play.google.com/store/apps/details?id=pl.agilesurfing.melbtram) is available for free in Google play store, check it out!