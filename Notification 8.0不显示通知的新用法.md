#### Android  8.0以上通知栏不显示


##### 通知栏使用重要的API

###### ==NotificationManager==   
###### ==Notification==   
###### ==NotificationChannel==



##### 最近在android 8.0的手机上发现通知栏不显示通知了！

``` javascript
No Channel found for pkg=camera.test.com.perssion, channelId=null, id=1, tag=null, opPkg=camera.test.com.perssion, 
callingUid=10367, userId=0, incomingUserId=0, notificationUid=10367, notification=Notification(channel=null pri=0 
contentView=null vibrate=null sound=null defaults=0x0 flags=0x0 color=0x00000000 vis=PRIVATE)
```
##### android 8.0之后添加了 推送通道，在8.0以上没有设置通道的话就会包上边的错误。可根据8.0以上设置chanle解决问题

``` javascript
   Notification notification;
        Notification.Builder builder;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
             builder=new Notification.Builder(this,CHANNEL_ID);
        }else {
             builder=new Notification.Builder(this);
        }
        //设置标题
        builder.setContentTitle("设置标题");
        //设置内容
        builder.setContentText("内容是............");
        //设置状态栏显示的图标，建议图标颜色透明
        builder.setSmallIcon(R.mipmap.ic_launcher);
        // 设置通知灯光（LIGHTS）、铃声（SOUND）、震动（VIBRATE）、（ALL 表示都设置）
        builder.setDefaults(Notification.DEFAULT_ALL);
        //灯光三个参数，颜色（argb）、亮时间（毫秒）、暗时间（毫秒）,灯光与设备有关
        builder.setLights(Color.RED, 200, 200);
        // 铃声,传入铃声的 Uri（可以本地或网上）我这没有铃声就不传了
        builder.setSound(Uri.parse("")) ;
        // 震动，传入一个 long 型数组，表示 停、震、停、震 ... （毫秒）
        builder.setVibrate(new long[]{0, 200, 200, 200, 200, 200});
        // 通知栏点击后自动消失
        builder.setAutoCancel(true);
        // 简单通知栏设置 Intent
        builder.setContentIntent(setPendingIntent("简单通知"));
        builder.setPriority(Notification.PRIORITY_HIGH);

        //设置下拉之后显示的图片
        builder.setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.icon));
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(CHANNEL_ID, CHANEL_NAME, NotificationManager.IMPORTANCE_DEFAULT);
            channel.enableLights(true);//是否在桌面icon右上角展示小红点
            channel.setLightColor(Color.GREEN);//小红点颜色
            channel.setShowBadge(false); //是否在久按桌面图标时显示此渠道的通知
            notificationManager.createNotificationChannel(channel);
        }
        notification=builder.build();
        notificationManager.notify(1,notification);
```

