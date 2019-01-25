### Android 6.0运行时权限
##### 1. 官网上介绍

###### 此版本引入了一种新的权限模式，如今，用户可直接在运行时管理应用权限。这种模式让用户能够更好地了解和控制权限，同时为应用开发者精简了安装和自动更新过程。用户可为所安装的各个应用分别授予或撤销权限。

###### 对于以 Android 6.0（API 级别 23）或更高版本为目标平台的应用，请务必在运行时检查和请求权限。要确定您的应用是否已被授予权限，请调用新增的 `checkSelfPermission() `方法。要请求权限，请调用新增的 `requestPermissions() `方法。即使您的应用并不以 Android 6.0（API 级别 23）为目标平台，您也应该在新权限模式下测试您的应用。

##### 2. 危险权限和普通权限

###### 在target 版本大于23的时候，并不是所有的权限都需要动态申请，只有系统认为是危险的权限，才需要去在用户使用到的时候去申请。

###### 使用 adb 工具从命令行管理权限

###### 按组列出权限和状态：
###### ==adb shell pm list permissions -d -g==

``` javascript
group:com.google.android.gms.permission.CAR_INFORMATION
  permission:com.google.android.gms.permission.CAR_VENDOR_EXTENSION
  permission:com.google.android.gms.permission.CAR_MILEAGE
  permission:com.google.android.gms.permission.CAR_FUEL

group:android.permission-group.CONTACTS
  permission:android.permission.WRITE_CONTACTS
  permission:android.permission.GET_ACCOUNTS
  permission:android.permission.READ_CONTACTS

group:android.permission-group.PHONE
  permission:android.permission.READ_CALL_LOG
  permission:android.permission.ANSWER_PHONE_CALLS
  permission:android.permission.READ_PHONE_NUMBERS
  permission:android.permission.READ_PHONE_STATE
  permission:android.permission.CALL_PHONE
  permission:android.permission.WRITE_CALL_LOG
  permission:android.permission.USE_SIP
  permission:android.permission.PROCESS_OUTGOING_CALLS
  permission:com.android.voicemail.permission.ADD_VOICEMAIL

group:android.permission-group.CALENDAR
  permission:android.permission.READ_CALENDAR
  permission:android.permission.WRITE_CALENDAR

group:android.permission-group.CAMERA
  permission:android.permission.CAMERA

group:android.permission-group.SENSORS
  permission:android.permission.BODY_SENSORS

group:android.permission-group.LOCATION
  permission:android.permission.ACCESS_FINE_LOCATION
  permission:com.google.android.gms.permission.CAR_SPEED
  permission:android.permission.ACCESS_COARSE_LOCATION

group:android.permission-group.STORAGE
  permission:android.permission.READ_EXTERNAL_STORAGE
  permission:android.permission.WRITE_EXTERNAL_STORAGE

group:com.sina.weibo.permission-group
  permission:com.sina.weibo.permission.USER

group:android.permission-group.MICROPHONE
  permission:android.permission.RECORD_AUDIO

group:android.permission-group.SMS
  permission:android.permission.READ_SMS
  permission:android.permission.RECEIVE_WAP_PUSH
  permission:android.permission.RECEIVE_MMS
  permission:android.permission.RECEIVE_SMS
  permission:android.permission.SEND_SMS
  permission:android.permission.READ_CELL_BROADCASTS

ungrouped:
  permission:org.fidoalliance.uaf.permissions.ACT_AS_WEB_BROWSER
  permission:com.google.android.providers.talk.permission.WRITE_ONLY
  permission:com.huawei.pushagent.permission.RICHMEDIA_PROVIDER
  permission:com.huawei.permission.ACCESS_FM
  permission:com.huawei.motion.permission.MOTION_EX
  permission:org.fidoalliance.uaf.permissions.FIDO_CLIENT
  permission:com.google.android.providers.talk.permission.READ_ONLY
  permission:com.huawei.hilinkapp.permission.ACTION_BROADCAST
  permission:com.huawei.contacts.permission.CHOOSE_SUBSCRIPTION

```

######  使用以下语法授予或撤销一项或多项权限：
###### ==adb shell pm [grant|revoke] <permission.name> ... #F44336==

##### 3. 申请权限的API

######     （1）.判断权限是否申请过
######  ==ContextCompat.checkSelfPermission==

######     （2）.申请权限
######  ==ActivityCompat.requestPermissions==

######     （3）.可以推断出用户选择了“不在提示”选项，在这种情况下需要引导用户至设置页手动授权
######  	==ActivityCompat.shouldShowRequestPermissionRationale==
**1.上次选择禁止并勾选：下次不在询问	false
2.第一次打开App时	 false
3.上次弹出权限点击了禁止（但没有勾选“下次不在询问”）	 true**

##### 4. 申请单个权限的例子

###### 1. 清单文件注册权限

``` javascript
<uses-permission android:name="android.permission.CALL_PHONE"></uses-permission>
```

###### 2. 布局文件

``` javascript
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="camera.test.com.perssion.MainActivity">

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="申请打电话动态权限"
        android:onClick="requsetCall"
        />

</LinearLayout>


```



###### 2. 申请权限的代码

``` javascript
package camera.test.com.perssion;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Build;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

    }

    /**
     * 申请打电话权限
     */
    int requestCodes = 1;

    public void requsetCall(View view) {
        //判断权限是否申请过

        //系统运行环境小于6.0不需要权限申请
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {
            Toast.makeText(this, "目标版本不是23不需要申请权限，去做想做的事情BA！", Toast.LENGTH_LONG).show();
            return;
        }
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
            //申请权限
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CALL_PHONE}, requestCodes);
        } else {
            Toast.makeText(this, "权限申请通过去做想做的事情把！", Toast.LENGTH_LONG).show();
        }
    }
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == requestCodes) {
            if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "权限申请通过去做想做的事情把！", Toast.LENGTH_LONG).show();
            } else {
                Toast.makeText(this, "权限没有申请通过！", Toast.LENGTH_LONG).show();
//                权限没有申请通过判断拒绝权限的情况
//                1.上次选择禁止并勾选：下次不在询问	false
//                2.第一次打开App时	 false
//                3.上次弹出权限点击了禁止（但没有勾选“下次不在询问”）	 true
                if (ActivityCompat.shouldShowRequestPermissionRationale(this, permissions[0])) {
                    //没有勾选不在询问的选项
                    Toast.makeText(this, "没有勾选不在询问的选项！重新去请求权限", Toast.LENGTH_LONG).show();
                    ActivityCompat.requestPermissions(this, new String[]{permissions[0]}, requestCodes);
                } else {
                    //勾选不在询问的选项
                    Toast.makeText(this, "勾选不在询问的选项！", Toast.LENGTH_LONG).show();
                }
            }

        }
    }
}


```
###### 4. 申请多个权限的例子

``` javascript
public String[] perssions = new String[]{
            Manifest.permission.CALL_PHONE,
            Manifest.permission.ACCESS_COARSE_LOCATION,
            Manifest.permission.WRITE_EXTERNAL_STORAGE
    };

    public void requsetMoreCall(View view) {
        List<String> perssionsList = new ArrayList<>();
        perssionsList.clear();
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {
            Toast.makeText(this, "目标版本不是23不需要申请权限，去做想做的事情BA！", Toast.LENGTH_LONG).show();
            return;
        }

        for (int i = 0; i < perssions.length; i++) {
            if (ActivityCompat.checkSelfPermission(this, perssions[i]) != PackageManager.PERMISSION_GRANTED) {
                perssionsList.add(perssions[i]);
            }
        }
        if (!perssionsList.isEmpty()) {
            ActivityCompat.requestPermissions(this, perssionsList.toArray(new String[perssionsList.size()]), requestCodeAll);
        } else {
            Toast.makeText(this, "权限申请全部申请通过去做想做的事情把！", Toast.LENGTH_LONG).show();
        }


    }
	
	  @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == requestCodeAll) {
            List<String> result = new ArrayList<>();
            for (int i = 0; i < grantResults.length; i++) {
                if (grantResults[i] != PackageManager.PERMISSION_GRANTED) {
                    result.add(permissions[i]);
                }
            }
            if (!result.isEmpty()) {
                //权限没有申请通过
                String[] requestperssions = result.toArray(new String[result.size()]);
                List<String> reRequestPerssion = new ArrayList<>();
                for (int i = 0; i < requestperssions.length; i++) {
                    if (ActivityCompat.shouldShowRequestPermissionRationale(this, requestperssions[i])) {
                        reRequestPerssion.add(requestperssions[i]);
                    }
                }
                if (!reRequestPerssion.isEmpty()) {
                    ActivityCompat.requestPermissions(this, result.toArray(new String[result.size()]), requestCodeAll);
                } else {
                    Toast.makeText(this, "勾选不在询问的选项111111！", Toast.LENGTH_LONG).show();
                }
            } else {
                Toast.makeText(this, "权限申请全部通过去做想做的事情把！", Toast.LENGTH_LONG).show();
            }

        }
    }
	

```


##### 5. 权限封装工具类

``` javascript
package camera.test.com.perssion.perssionUtils;

import android.app.Activity;
import android.content.pm.PackageManager;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by zhang on 2018/12/19.
 */

public class PermissionUtils {

    /**
     * 普通的拒绝，没有勾选不在提示
     */
    public static final int REQUESTFLAGDENIED = 2;
    /**
     * 拒绝勾选了不在提示
     */
    public static final int REQUESTFLAGDENIEDNOASK = 3;

    /**
     * requestPermissions single
     * shouldShowRequestPermissionRationale
     * 1.上次选择禁止并勾选：下次不在询问	false
     * 2.第一次打开App时	 false
     * 3.上次弹出权限点击了禁止（但没有勾选“下次不在询问”）	 true
     *
     * @param mActivity
     * @param permission
     * @param requestCode
     * @param requestPermissionsCallBcak
     */
    public static void requestPermissions(Activity mActivity, String permission, int requestCode, RequestPermissionsCallBcak requestPermissionsCallBcak) {
        //判断权限是否开启
        if (ContextCompat.checkSelfPermission(mActivity, permission) != PackageManager.PERMISSION_GRANTED) {
            mActivity.requestPermissions(new String[]{permission}, requestCode);
        } else {
            //已经拥有权限，可以做自己想做的操作
            requestPermissionsCallBcak.requestPermissionsSucesss(requestCode);
        }
    }

    /**
     * 单个权限提示
     *
     * @param requestCode
     * @param permissions
     * @param grantResults
     * @param requestPermissionsCallBcak
     */
    public static void onRequestPermissionsResult(Activity mActivity, int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults, RequestPermissionsCallBcak requestPermissionsCallBcak) {
        if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            //权限开启成功
            requestPermissionsCallBcak.requestPermissionsSucesss(requestCode);
        } else {
            //权限开始失败
            if (!ActivityCompat.shouldShowRequestPermissionRationale(mActivity, permissions[0])) {
                requestPermissionsCallBcak.requestPermissionfailure(REQUESTFLAGDENIED, requestCode);
            } else {
                requestPermissionsCallBcak.requestPermissionfailure(REQUESTFLAGDENIEDNOASK, requestCode);
            }
        }
    }

    /**
     * 申请多个权限的问题
     *
     * @param mActivity
     * @param permissions
     * @param requestCode
     * @param requestPermissionsCallBcak
     */
    public static void requestPermissions(Activity mActivity, String[] permissions, int requestCode, RequestPermissionsCallBcak requestPermissionsCallBcak) {
        List<String> permissionLists = new ArrayList<>();
        for (String permission : permissions) {
            if (ContextCompat.checkSelfPermission(mActivity, permission) != PackageManager.PERMISSION_GRANTED) {
                permissionLists.add(permission);
            }
        }
        if (!permissionLists.isEmpty()) {
            ActivityCompat.requestPermissions(mActivity, permissionLists.toArray(new String[permissionLists.size()]), requestCode);
        } else {
            //表示全都授权了
            requestPermissionsCallBcak.requestPermissionsSucesss(requestCode);
        }
    }

    /**
     * 多个权限
     *
     * @param requestCode
     * @param permissions
     * @param grantResults
     * @param requestPermissionsCallBcak
     */
    public static void onRequestPermissionsResults(Activity mActivity, int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults, RequestPermissionsCallBcak requestPermissionsCallBcak) {
        if (grantResults.length > 0) {
            //存放没授权的权限
            List<String> deniedPermissions = new ArrayList<>();
            for (int i = 0; i < grantResults.length; i++) {
                if (grantResults[i] != PackageManager.PERMISSION_GRANTED) {
                    deniedPermissions.add(permissions[i]);
                }
            }
            if (deniedPermissions.isEmpty()) {
                requestPermissionsCallBcak.requestPermissionsSucesss(requestCode);
            } else {
                for (String deniedPermission : deniedPermissions) {
                    if (ActivityCompat.shouldShowRequestPermissionRationale(mActivity, deniedPermission)) {
                        requestPermissionsCallBcak.requestPermissionfailure(REQUESTFLAGDENIED, deniedPermissions);
                        return;
                    }
                }
                requestPermissionsCallBcak.requestPermissionfailure(REQUESTFLAGDENIEDNOASK, deniedPermissions);
            }
        }
    }

}

```

###### 回调接口

``` javascript
package camera.test.com.perssion.perssionUtils;

/**
 * Created by zhang on 2018/12/19.
 */

public interface RequestPermissionsCallBcak<T extends Object> {
    /**
     * 权限申请成功回调
     */
    void requestPermissionsSucesss(int requestCode);

    /**
     * 权限申请失败回调
     */
    void requestPermissionfailure(int errorCode, T requestCode);
}

```


##### 6. 权限申请工具类使用

###### （1） Acticity 实现接口
###### （2） 调用工具类中的权限申请方法 
  ==PermissionUtils.requestPermissions(this,perssions,3,this);==

###### （3） onRequestPermissionsResult   中调用
==PermissionUtils.onRequestPermissionsResults==

###### （4） RequestPermissionsCallBcak   接口中处理权限成功失败的操作
 ==失败操作 errorCode 普通拒绝，可以在申请权限，errorCode=3标识权限拒绝，并且点击了不再提示==


