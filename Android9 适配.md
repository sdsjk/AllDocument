### Android  9  http��������������


#### 1. ��Android 9�ϣ�Ϊ���û� ����˽��ֹʹ��httpȥ������ҪHttpsȥ����������ô��ԭ����http����ʹ���أ�

##### ����һ��

``` javascript
 android:networkSecurityConfig="@xml/network_security_config"

```
��Appliaction ��ǩ������������


##### ����� ��

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

��res/xmlĿ¼���½��ļ���


#### 2 �� Android 8.0��Ӧ�ø��°�װ ��ת����װ����ʧ��
����������һ��Ȩ��

``` javascript
 <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
```

#### 3 ��Android 8.0�ϳ�������

��ֹactivity���ú�����ȥ��������Ļ������

``` javascript
android:screenOrientation="portrait"
```

#### 4�� ҳ�������С�����������Ͻ�
�����������ҳ��onScaleChanged ֵ�ı仯

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

#### 5�� andrroid 7���ϳ��ָ��°�װ��ʾ��δ�ҵ���ִ�е�Ӧ����ʾ��

�ο����ϣ�https://www.e-learn.cn/content/wangluowenzhang/25513

##### ������Ϣ

``` javascript
	android.os.FileUriExposedException: file:///storage/emulated/0/test.txt exposed beyond app through Intent.getData()
```

���������
##### 1. ��Application��onCreate���������

``` javascript
	StrictMode.VmPolicy.Builder builder = new StrictMode.VmPolicy.Builder();
        StrictMode.setVmPolicy(builder.build());
```
##### 2. ��Android�嵥�ļ������Ȩ��

``` javascript
	 <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
```

#### 6�� ��������֮��֪ͨ������ʾ

