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

#### 5、 andrroid 7以上出现更新安装提示“未找到可执行的应用提示”

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

#### 6、 更新引擎之后通知栏不显示

