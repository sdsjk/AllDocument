### Android  9  http适配遇到的问题


#### 1. 在Android 9上，为了用户 的隐私禁止使用http去请求，需要Https去网络请求，怎么是原来的http还能使用呢？

##### 步骤一：

``` javascript
 android:networkSecurityConfig="@xml/network_security_config"

```
在Appliaction 标签中添加相关配置


##### 步骤二 ：

``` javascript
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>

```

在res/xml目录下新建文件。


#### 2 、 Android 8.0上应用更新安装 跳转到安装界面失败
解决方法添加一下权限

``` javascript
 <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
```

#### 3 、Android 8.0上程序闪退

禁止activity设置横竖屏去掉设置屏幕的配置

``` javascript
android:screenOrientation="portrait"
```

#### 4、 页面出现缩小，浮动到左上角
解决方法监听页面onScaleChanged 值的变化

``` javascript
@Override
    public void onScaleChanged(WebView view, float oldScale, float
            newScale) {
        String windowName = null;
        if (view instanceof EBrowserView){
            windowName = ((EBrowserView) view).getName();
            notifyScaleChangedToJS((EBrowserView) view);
            BDebug.i("windowName = " + windowName + " oldScale = " + oldScale + " newScale = " + newScale);
        }
    }

    private void notifyScaleChangedToJS(EBrowserView webview){
        String js = "javascript:if(window.onresize){window.onresize()}else{console.log('AppCanEngine-->notifyScaleChangedToJS else')}";
        webview.addUriTask(js);
    }
```

#### 5、 andrroid 7以上出现更新安装提示“未找到可执行的应用提示”, 子应用是源生应用，点击子应用闪退

参考资料：https://www.e-learn.cn/content/wangluowenzhang/25513

##### 错误信息

``` javascript
	android.os.FileUriExposedException: file:///storage/emulated/0/test.txt exposed beyond app through Intent.getData()
```

解决方法：
##### 1. 在Application的onCreate方法中添加

``` javascript
	StrictMode.VmPolicy.Builder builder = new StrictMode.VmPolicy.Builder();
        StrictMode.setVmPolicy(builder.build());
```
##### 2. 在Android清单文件中添加权限

``` javascript
	 <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
```

#### 6、 QQ插件分享崩溃的问题


``` javascript

	Android9.0_P:ClassNotFoundException: Didn't find class "org.apache.http.conn.scheme.SchemeRegistry"
```
###### Apache HTTP 客户端弃用

在 Android 6.0 中，我们取消了对 Apache HTTP 客户端的支持。 从 Android 9 开始，默认情况下该内容库已从 bootclasspath 中移除且不可用于应用。

要继续使用 Apache HTTP 客户端，以 Android 9 及更高版本为目标的应用可以向其 AndroidManifest.xml的application节点下 添加以下内容：

<uses-library android:name="org.apache.http.legacy" android:required="false"/>


#### 7、 子应用有新版本但是没有版本提示更新，

使用最新的EMM插件，可能是子应用没有启动上报，具体原因不清楚

#### 8 、QQ插件在华为meta8 Android 8.1上崩溃

崩溃日志：

``` javascript

	java.lang.IllegalStateException: Only fullscreen opaque activities can request orientation

```
activty在8.0上禁止设置屏幕的方向属性


#### 9 、打包失败

``` javascript

com.android.dex.DexException: Multiple dex files define Lcom/slidingmenu/lib/CustomViewAbove;
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.merge.DexMerger.readSortableTypes(DexMerger.java:596)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.merge.DexMerger.getSortedTypes(DexMerger.java:554)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.merge.DexMerger.mergeClassDefs(DexMerger.java:535)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.merge.DexMerger.mergeDexes(DexMerger.java:171)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.merge.DexMerger.merge(DexMerger.java:189)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.command.dexer.Main.mergeLibraryDexBuffers(Main.java:502)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.command.dexer.Main.runMonoDex(Main.java:334)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.command.dexer.Main.run(Main.java:277)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.command.dexer.Main.main(Main.java:245)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 	at com.android.dx.command.Main.main(Main.java:106)
[2019-01-04 14:32:33,514] DEBUG Thread-481 //opt/local/sdksuit-core/output/logs//aaabz10015-android -        [dx] 

```

jar重复了删除重复的jar




#### 10 、极光推送插件点击通知栏没有执行相关操作

极光推送插件监听到极光推送过来的广播之后判断应用程序是否在前台，如果不在前台就启动APP，然后在发送一条广播，在MyReceiver中接受到广播
进行相关的Action处理。
######在android 8.0之后广播限制
如果应用注册为接收广播，则在每次发送广播时，应用的接收器都会消耗资源。 如果多个应用注册为接收基于系统事件的广播，这会引发问题；触发广播的系统事件会导致所有应用快速地连续消耗资源，从而降低用户体验。
为了缓解这一问题，Android 7.0（API 级别 25）对广播施加了一些限制，如后台优化中所述

实际理解就是不支持发送隐式广播


``` javascript

	  intent2.setComponent(new ComponentName(context.getPackageName(),"org.zywx.wbpalmstar.widgetone.uexjpush.receiver.MyReceiver"));

```
