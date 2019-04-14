---
layout: post
title: "Payload feature for developers"
date: 2015-04-22 17:20:00 +1
comments: true
tags: sdk
---
# Payload feature is your developers best friend

The payload feature in the beacon management frontend enables you to implement virtually unlimited use-case posibilities with beacons. We will highlight one use case in this post:

## Use case description:
We would like to show a notification when the application is in the background with a message. Upon tapping the notification, the app should be opened and a new Activity (screen) should be shown with a QR code and some text. This can be used to deliver a coupon code when entering a shop.

<!--more-->
**Setup your campaign**

<img alt="Screenshot from manage.sensorberg.com" src="/images/posts/2015-04-22-Unlimited-Use-Cases-With-Action-Payload/management-setup_001.png" width="100%" style="img-responsive center-block" />


I´ve setup my campaign as an in-app campaign with some custom payload. The payload contains the actual content that will be rendered when you tap the notification, or when you´re using the app:

{% highlight json %}
{
  "title": "Save 17%",
  "couponCode": "3A2601DD-C508-4AB5-9FD4-201B3A5361E3",
  "message": "Show this QR code at the cashier."
}
{% endhighlight %}
The **Subject** and **Body** will be used to construct the Notification. I use the URL *coupon://redeem* to identify this use-case.

# The Result

Here is how it looks when the app is in the foreground and background:

<iframe width="400" height="600" src="https://www.youtube.com/embed/8T7g2m5nRFM?autoplay=1&loop=1&rel=0&playlist=8T7g2m5nRFM" frameborder="0" allowfullscreen></iframe><iframe width="400" height="600" src="https://www.youtube.com/embed/7ltTuaSC8uI?autoplay=1&loop=1&rel=0&playlist=7ltTuaSC8uI" frameborder="0" allowfullscreen></iframe>

# The code

The most complicated part propably is the notification:
{% highlight java %}
		case MESSAGE_IN_APP:
                InAppAction inAppAction = (InAppAction) action;
                if (inAppAction.getUri().getScheme().equalsIgnoreCase("coupon") && inAppAction.getUri().getHost().equalsIgnoreCase("redeem")) {
                    try {
                        JSONObject p = action.getPayloadJSONObject();
                        PendingIntent pendingIntent =
                                TaskStackBuilder.create(context)
                                        .addNextIntentWithParentStack(new Intent(context, ShowcaseFragmentActivity.class))
                                        .addNextIntentWithParentStack(CouponActivity.getIntent(context, p.getString("couponCode"), p.getString("title"), p.getString("message") ))
                                        .getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
                        NotificationCompat.Builder builder = new NotificationCompat.Builder(context)
                                .setContentIntent(pendingIntent)
                                .setContentTitle(inAppAction.getSubject())
                                .setContentText(inAppAction.getBody())
                                .setAutoCancel(true)
                                .setShowWhen(true);
                        if (icon != null){
                            builder.setLargeIcon(icon);
                        } else {
                            builder.setSmallIcon(R.drawable.ic_launcher);
                            builder.setLargeIcon(BitmapFactory.decodeResource(context.getResources(), R.drawable.ic_launcher));
                        }
                        NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
                        notificationManager.notify(action.getUuid().hashCode(), builder.build());
                        return;
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
                }
{% endhighlight %}


Here is the Activity:
{% highlight java %}
public class CouponActivity extends Activity {


    public static final String EXTRA_BARCODE = "com.sensorberg.android.showcaseutils.Activities.CouponActivity.barcode";
    public static final String EXTRA_TITLE = "com.sensorberg.android.showcaseutils.Activities.CouponActivity.title";
    public static final String EXTRA_MESSAGE = "com.sensorberg.android.showcaseutils.Activities.CouponActivity.message";


    private String barcode;
    private String title;
    private String message;

    private TextView titleTextView;
    private TextView messageTextView;
    private ImageView barcodeTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.coupon_activity);

        titleTextView = (TextView) findViewById(R.id.title_textView);
        messageTextView = (TextView) findViewById(R.id.message_textView);
        barcodeTextView = (ImageView) findViewById(R.id.barcode_imageView);
    }

    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        barcode = intent.getStringExtra(EXTRA_BARCODE);
        title = intent.getStringExtra(EXTRA_TITLE);
        message = intent.getStringExtra(EXTRA_MESSAGE);
        updateViews();
    }

    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = getIntent();
        barcode = intent.getStringExtra(EXTRA_BARCODE);
        title = intent.getStringExtra(EXTRA_TITLE);
        message = intent.getStringExtra(EXTRA_MESSAGE);
        updateViews();
        getActionBar().setDisplayHomeAsUpEnabled(true);
    }

    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case android.R.id.home:
                this.finish();
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }

    private void updateViews() {
        if (barcode != null) {
            QRCodeWriter writer = new QRCodeWriter();
            try {
                Map<EncodeHintType, Object> options = new HashMap<>();
                options.put(EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.H);

                BitMatrix bitMatrix = writer.encode(barcode, BarcodeFormat.QR_CODE, 768, 768, options);
                int width = bitMatrix.getWidth();
                int height = bitMatrix.getHeight();
                Bitmap bmp = Bitmap.createBitmap(width, height, Bitmap.Config.RGB_565);
                for (int x = 0; x < width; x++) {
                    for (int y = 0; y < height; y++) {
                        bmp.setPixel(x, y, bitMatrix.get(x, y) ? Color.BLACK : Color.WHITE);
                    }
                }
                barcodeTextView.setImageBitmap(bmp);

            } catch (WriterException e) {
                e.printStackTrace();
            }
        } else{
            barcodeTextView.setImageBitmap(null);
        }

        titleTextView.setText(title);
        messageTextView.setText(message);
    }

    public static Intent getIntent(Context context, String couponCode, String title, String message) {
        Intent intent = new Intent(context, CouponActivity.class);
        intent.putExtra(EXTRA_BARCODE, couponCode);
        intent.putExtra(EXTRA_TITLE, title);
        intent.putExtra(EXTRA_MESSAGE, message);
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        return intent;
    }
}
{% endhighlight %}
And the layout for this Activity:
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="8dp"
    android:weightSum="1">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        tools:text="Redeem this coupon"
        android:id="@+id/title_textView" />

    <ImageView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/barcode_imageView"
        android:layout_gravity="center_horizontal"
        android:layout_weight="0.55" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        tools:text="30€"
        android:id="@+id/message_textView"
        android:layout_gravity="center_horizontal" />
</LinearLayout>
{% endhighlight %}
