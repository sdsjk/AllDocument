@[TOC]

# Android 插件开发指南

# 1.简介
本文档主要介绍了如何实现Android平台原生插件。在您阅读此文档时，我们假定您已经具备了基础的Android应用开发经验，并能够理解相关基础概念。此外,您也应该对HTML,JavaScript,CSS等有一定的了解,并且熟悉在JavaScript和java环境下的JSON格式数据操作。以开发一个范例插件ACPluginDemo为例，实现JavaScript与原生功能之间的互相调用。

Android插件入口类必须继承ACPluginBase类。JavaScript的方法需要配置到plugin.xml中，其对应的原生方法必须在入口类中提供实现。

# 2. 开发环境
已经有Android开发环境的可以略过本段

Android的开发环境搭建主要包括JDK、Android Studio的安装。

- 2.1.JDK安装验证
安装完成之后，可以在检查JDK是否安装成功。打开终端，输入java –version 查看JDK的版本信息。出现类似下面的画面表示安装成功了：

![](https://i.imgur.com/KS0D3wn.png)

- 2.2.Android Studio
Android Studio 的安装教程网上有很多，请自行搜索一下，可能需要使用代理

官网地址：https://developer.android.com/studio/index.html

- 2.3.插件源码
点击下载Demo源码

# 3. 配置工程

- 3.1.新建工程
Android Studio中选择File->New->New Project

![](https://i.imgur.com/b5uiIog.png)

填好应用名称和包名

![](https://i.imgur.com/sXYYAhc.png)

点击next，选择Add No Activity

![](https://i.imgur.com/TkdSjAM.png)

点击“完成”，切换下Project视图，这里以Project视图为例。

![](https://i.imgur.com/zT77TPs.png)

修改app模块名称为"ACPluginDemo"

![](https://i.imgur.com/pBIfQq3.png)

![](https://i.imgur.com/YsEStmt.png)

删除AndroidManifest.xml中的其他信息，只保留如下图所示的信息。

![](https://i.imgur.com/MLGxlWr.png)

删除如下图所示红色框框内的文件夹或文件。

![](https://i.imgur.com/jDhOxNR.png)

删除之后，ACPluginDemo的目录结构如下图:

![](https://i.imgur.com/aguroCE.png)

- 3.2.引入引擎
导入ACWeEngineSDK模块，如下图：

![](https://i.imgur.com/e8HJI0a.png)

![](https://i.imgur.com/uuBMttg.png)

ACPluginDemo工程的根目录下面gradle.properties文件（如果没有就创建一个），添加如下代码：

	org.gradle.daemon=true
	org.gradle.parallel=true
	org.android.dexOptions.preDexLibraries=false
	android.enableBuildCache=false
	org.gradle.jvmargs=-Xmx4096m
	#android.buildCacheDir=./build/buildCache/
	#android.enableAapt2=false
	#android.injected.build.model.only.versioned = 3
	#android.injected.testOnly=false
	
	TINKER_VERSION=1.7.11
	TINKERPATCH_VERSION=1.1.7
	MARVEN_PATH = http://39.155.221.35:60127/nexus/content/repositories/releases/
	ACCOUNT = admin
	PASSWORD = admin123

在ACPluginDemo工程根目录下面的build.gradle文件中添加如下所示代码：

	// Top-level build file where you can add configuration options common to all sub-projects/modules.
	buildscript {
	    ext.kotlin_version = '1.2.51'
	    ext.kotlin_version = '1.1.3-2'
	    ext.anko_version = '0.10.0'
	    repositories {
	        jcenter()
	        maven{ url 'http://jcenter.bintray.com'}
	
	        maven {
	            url MARVEN_PATH
	        }
	        google()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:3.2.0'
	//        classpath 'com.android.tools.build:gradle:3.1.3'
	        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
	        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.0'
	        classpath 'com.jakewharton:butterknife-gradle-plugin:8.4.0'
	        classpath 'me.xx2bab.gradle:seal-manifest-precheck-plugin:1.0.0'
	        // NOTE: Do not place your application dependencies here; they belong
	        // in the individual module build.gradle files
	        classpath 'com.appcan.engine:ACScript:1.0.0'
	    }
	}
	//客户端版本号，默认为1.0
	String version = "1.0";
	//打包脚本命令行传参，如果有传version参数，则使用传参的版本。该参数在主工程目录下的build.gradle脚本文件中用到。
	if ( project.hasProperty("version") ) {
	    version = project.getProperty("version")
	}
	//定义的全局变量，在子项目中，可以使用   rootProject.ext.xxx的方式获取。例如rootProject.ext.versionName可以获取versionName的值
	//主要定义编译环境，让各个项目的编译环境一致，避免各个环境使用的sdk版本不一致，而可能导致编译失败。以后需要更改编译环境，在此更改就好了
	ext {
	    compileSdkVersion = 27
	    buildToolsVersion = "27.0.3"
	    targetSdkVersion = 27
	    minSdkVersion = 19
	    versionCode = 1
	    versionName = "${version}"
	    kotlin_version = "1.1.3-2"
	    anko_version = '0.8.2'
	}
	
	
	allprojects {
	    configurations.all {
	        resolutionStrategy.force 'com.android.support:support-v4:27.1.1'
	    }
	    repositories {
	//        jcenter()
	        mavenCentral()
	        maven{ url 'http://jcenter.bintray.com'}
	        //添加插件marven库
	        maven {
	
	            url MARVEN_PATH
	
	        }
	        maven { url 'https://dl.bintray.com/jetbrains/anko' }
	
	        flatDir {
	            dirs '../../aar'
	        }
	//        google()
	        maven {
	            url 'https://dl.google.com/dl/android/maven2/'
	        }
	
	        maven {
	            url  'http://dl.bintray.com/piasy/maven'
	        }
	
	        maven { url "https://jitpack.io" }
	
	    }
	}
	
	//task clean(type: Delete) {
	//    delete rootProject.buildDir
	//}

在ACPluginDemo工程下的build.gradle文件中添加如下图所示代码：

	repositories {
	    jcenter()
	    maven {
	        url MARVEN_PATH
	    }
	}
	apply plugin: 'maven'
	buildscript {
	    repositories {
	        jcenter()
	        maven {
	            url "http://39.155.221.35:60127/nexus/content/repositories/releases/"
	        }
	        google()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:2.3.3'
	        classpath 'com.appcan.engine:ACScript:1.0.0'
	        //classpath fileTree(dir: '../gradle', include: '*.jar')
	    }
	}
	
	apply plugin: 'com.android.application'
	//apply plugin: 'com.android.library'
	apply plugin: 'maven'
	
	android {
	    compileSdkVersion rootProject.ext.compileSdkVersion
	    buildToolsVersion '28.0.2'
	    defaultConfig {
	        applicationId "com.ac.plugindemo"
	        minSdkVersion rootProject.ext.minSdkVersion
	        targetSdkVersion rootProject.ext.targetSdkVersion
	        versionCode rootProject.ext.versionCode
	        versionName rootProject.ext.versionName
	        javaCompileOptions { annotationProcessorOptions { includeCompileClasspath = true } }
	    }
	    buildTypes {
	        release {
	            minifyEnabled false
	            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard.pro'
	        }
	    }
	}
	
	repositories {
	    flatDir {
	        dirs 'libs'
	    }
	    jcenter()
	    maven {
	        url "${MARVEN_PATH}"
	    }
	}
	
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
	    //compile(name: 'ACWeEngineSDK-release', ext: 'aar')
	    compile project(':ACWeEngineSDK')
	
	}


在AndroidManifest.xml文件中添加如下代码：

	<application
        android:name="com.appcan.engine.WidgetOneApplication">

        <activity android:name="com.appcan.engine.ui.browser.webview.ACBrowserActivity"
            android:theme="@style/AppTheme.NoActionBar"
            android:hardwareAccelerated="false">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

入口application和启动activity，这两个必须添加，否则运行异常。

然后点击“Sync Now”按钮，等待同步完成。

备注：ACWeEngineSDK也可通过maven远程依赖，或者打包arr依赖

# 4.插件开发

- 4.1.接口配置
创建插件入口类，在src->main->java目录下，新建class，取名"ACPluginDemo"，继承自ACPluginBase类。

![](https://i.imgur.com/Bxa2zID.png)

并且需要实现clean()方法，同时也要定义构造方法，在该构造方法中进行一些初始化操作。相关代码如下:

	public class ACPluginDemo extends ACPluginBase {
 
	    public ACPluginDemo(Context context, EBrowserView eBrowserView) {
	        super((ACBrowserActivity) context, eBrowserView);
	    }
		    @Override
	    protected boolean clean() {
	        return false;
	    }
	}

紧接着在res目录下新建xml文件夹，在xml文件夹下新建plugin.xml文件，文件内容如下:

	<?xml version="1.0" encoding="utf-8"?>
		<corplugins>
		    <plugin
		       className="com.ac.plugindemo.ACPluginDemo" corName="acPluginDemo">
		        <method name="test_startActivityForResult" />
		    </plugin>
		</corplugins>

其中className对应继承自ACPluginBase的ACPluginDemo类的完整路径。corName指定插件名称，即html网页中调用该插件时的对象名称。method指定方法名称，本例中指定方法名称为test_startActivityForResult.

- 4.2.接口实现
test_startActivityForResult方法启动一个Activity，该方法需要在插件入口类中定义。代码如下:

		  public static final String KEY_RESULT = "result";
		  private int startActivityForResultFunId;//回调函数
		  private static final int TEST_ACTIVITY_REQUEST_CODE = 1;
		  /**
		     * 启动一个activity并且在结束时回调相关信息给网页
		     * @param param 传入数组参数
		     * @return int 打开activity成功与否
		     */
	    public int test_startActivityForResult(String[] param) {
	        if (param.length > 0) {
	            startActivityForResultFunId = Integer.parseInt(param[0]);
	        }
	        Intent intent = new Intent();
	        intent.setClass(mContext, HelloCorPluginActivity.class);
	        try {
	            startActivityForResult(intent, TEST_ACTIVITY_REQUEST_CODE);
	            return CorCallback.F_C_SUCCESS;
	        } catch (Exception e) {
	            return CorCallback.F_C_FAILED;
	        }
	    }

	    @Override
	    public void onActivityResult(int requestCode, int resultCode, Intent data) {
	        if (requestCode == TEST_ACTIVITY_REQUEST_CODE) {
	            JSONObject jsonObject = new JSONObject();
	            int errorCode;
	            try {
	                if (resultCode == Activity.RESULT_OK) {
	                    String ret = data.getStringExtra(KEY_RESULT);
	                    jsonObject.put(KEY_RESULT, ret);
	                } else {
	                    jsonObject.put(KEY_RESULT, "press back key");
	                }
	                errorCode = CorCallback.F_C_SUCCESS;
	            } catch (JSONException e) {
	                e.printStackTrace();
	                errorCode = CorCallback.F_C_FAILED;
	            }
	            if(startActivityForResultFunId != -1){
	                callbackToJs(startActivityForResultFunId, false, errorCode, jsonObject);
	            }
	        }
	    }

其中回调给网页的方法callbackToJs说明如下:

    /**
     * 异步回调到JS
     * @param callbackId 回调ID，该值由插件接口被调用时传入
     * @param hasNext 是否有下一次回调。没有传false ，有传true
     * @param args 参数可以是任何对象，直接回调对象可使用DataHelper.gson.toJsonTree()方法
     */
    @Keep
    public void callbackToJs(int callbackId,boolean hasNext,Object... args){
    }

**注意：**test_startActivityForResult默认执行在主线程中，如果需要同步返回结果，可以直接定义返回值，如本例中的int返回值。若需要异步返回结果，则需要定义回调函数接收返回结果。通过调用入口类中继承自父类的callbackToJs方法异步回调给网页。

新建HelloACPluginActivity类继承自Activity
	
![](https://i.imgur.com/sFAgU3s.png)

	public class HelloCorPluginActivity extends Activity {
	    @Override
	    protected void onCreate(@Nullable Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.cordemo_main);
	    }
	
	    public void doFinish(View view){
	        Intent it = new Intent(getIntent().getAction());
	        it.putExtra(CorPluginDemo.KEY_RESULT,
	                "this is result from activity");
	        setResult(Activity.RESULT_OK, it);
	        finish();
	    }
	}

并且将该activity在AndroidManifest.xml中注册，如下:

	<activity android:name="com.ac.plugindemo.HelloACPluginActivity"/>

![](https://i.imgur.com/NiNhi3v.png)

- 4.3.接口调用
在src->main目录下新建assets文件夹，assets下新建widget文件夹，在widget下新建config.xml文件，如下：

![](https://i.imgur.com/kJGSPYi.png)

config.xml文件中代码如下：

	<?xml version="1.0" encoding="utf-8" ?>
	<widget widgetId="" appId="" channelCode="" version="0.1">
   		<content src="index.html" encoding="utf-8"/>
	</widget>


其中src属性指定启动页html，本例中启动页为config.xml相同目录下的index.html网页，目录结构如下图：

![](https://i.imgur.com/yFukN7Q.png)

在index.html页面中调用test_startActivityForResult方法。调用方法如下:

    function test_startActivityForResult() {
        var ret = acPluginDemo.test_startActivityForResult(function(error, data) {
            if (!error){
                alert("返回结果是:"+data.result);
            }
        });
        console.log("test_startActivityForResult:" + (ret == 0 ? "启动成功" : "启动失败"));
    }


- 4.4.运行效果
至此，可以点击运行CorPluginDemo了，运行效果如下图:

<img src="https://i.imgur.com/EYaaRWx.gif" width="300" hegiht="400" align=center />

# 5.插件目录结构
完整插件至少包含以下结构信息：

	ACPluginDemo//模块名称
	│  build.gradle//脚本文件
	│  ACPluginDemo.iml
	│  proguard.pro
	├─aar//本地aar存放目录
	├─libs
	└─src
	    └─main
	        │  AndroidManifest.xml
	        ├─assets
	        │  └─widget//此处存放前端html代码
	        │      │  config.xml//配置文件，起始页及相关配置在该文件中
	        │      │  index.html
	        │      └─css
	        │              index.css
	        ├─java
	        │  └─com
	        │      └─ac
	        │          └─plugindemo
	        │              ACPluginDemo.java
	        │              HelloACPluginActivity.java
	        │                      
	        └─res
	            ├─layout
	            │      acplugindemo_main.xml
	            ├─values
	            │      strings.xml
	            └─xml
	                    plugin.xml//插件方法的配置信息

# 6.注意事项

- 6.1.数据传递

   - 6.1.1.前端传递数据到插件
前端传递数据是通过接口入参的方式，定义一个接口方法时，需要同时确定该方法需要接收的参数，插件方法在入口类的定义时，必须是如下格式:

			public 返回值 方法名称(String[] param) {}

其中的param即是前端传递给插件的参数，该参数为一个String数组，**注意此处，声明方法时必须要包含该参数，无论需不需要相互传递参数。**

String数组的第一个参数特定为一个json字符串,该参数定义前端需要传递给插件的数据，第二个参数为回调方法(后续介绍)。

前端传入的数据插件需要做如下处理:

	public void intoTest(String[] param) {
	    String data = param[0];
	    ViewDataVO mAddViewData = DataHelper.gson.fromJson(parm[0], ViewDataVO.class);//定义一个和json字段对应的JavaBean，然后通过gson库的fromJson方法自动将json字符串转换为java对象使用。
	}

ViewDataVO.class代码如下:

	public class ViewDataVO implements Serializable{
	
	    private static final long serialVersionUID = 1194828283585702120L;
	
	    private double left;
	    private double top;
	    private double width;
	    private double height;
	
	    private boolean isScrollWithWebView = true;
	
	    public int getLeft() {
	        return (int) left;
	    }
	
	    public void setLeft(double left) {
	        this.left = left;
	    }
	
	    public int getTop() {
	        return (int) top;
	    }
	
	    public void setTop(double top) {
	        this.top = top;
	    }
	
	    public int getWidth() {
	        return (int) width;
	    }
	
	    public void setWidth(double width) {
	        this.width = width;
	    }
	
	    public int getHeight() {
	        return (int) height;
	    }
	
	    public void setHeight(double height) {
	        this.height = height;
	    }
	
	    public boolean isScrollWithWebView() {
	        return isScrollWithWebView;
	    }
	
	    public void setIsScrollWithWebView(boolean isScrollWithWebView) {
	        this.isScrollWithWebView = isScrollWithWebView;
	    }
	}

前端传入如下：

	acPluginDemo.intoTest({
	    left:0,
	    top:40,
	    width:500,
	    height:500,
	    isScrollWithWebView:false
	});

   - 6.1.2.插件传递数据到前端
       - 6.1.2.1.返回值方式
同步返回数据可以定义返回值。在插件入口类中定义方法时，定义返回值。插件中定义如下:

			public int getRet(String[] param) {
			    return 0;
			}

前端调用如下：

	var ret = acPluginDemo.getRet();//其中acPluginDemo为插件名称
	alert(ret);

      - 6.1.2.2.回调方式
回调方式主要用于异步返回数据，根据入参中介绍的第二个参数即是传入接收回调的方法，一般回调方法定义两个参数，第一个是errorCode，int类型，0表示成功，其他表示错误码，第二个是errorString(errorCode非0时，错误信息描述)或者data(object类型，errorCode为0返回的有效数据对象)。插件中处理如下：

	private int callbackFunId;
	public void callbackTest(String[] param) {
	    if (param.length > 0) {//为了代码便于阅读和扩展性强，建议第一个参数为入参，第二个参数为回调方法，即使暂时不需要传入参数，第一个参数的位置也需要留出来，因此在读取回调方法的时候可以取数组的最后一项，而非数组第二项。
	        callbackFunId = Integer.parseInt(param.length-1);
	    }
	    callbackToJs(callbackFunId, false, 0, "callbackTest");//回调到网页的方法，该方法可以在插件的任意位置调用。
	}

前端调用如下:

	acPluginDemo.callbackTest(function(error, data){
	    if(!error){
	        alert(data);
	    }
	});

参数对应关系如下图所示:

![](https://i.imgur.com/uacC5GT.png)

- 6.2.so文件
Android Studio中引用so文件存放在模块的src->main->jniLibs目录下，该目录下包含各个虚拟机对应的so版本，但是必须包含默认的armeabi文件夹，**注意在该插件打包上传时只能包含这一个文件夹，其余文件夹删掉，否则会导致运行失败。**

- 6.3.系统方法实现
引擎中提供一些系统方法调用时反射调用插件方法的机制，该机制目前支持如下方法：

	    public static void onApplicationCreate(Context context)
	    public static void onActivityCreate(Context context)
	    public static void onActivityStart(Context context)
	    public static void onActivityReStart(Context context)
	    public static void onActivityResume(Context context)
	    public static void onActivityPause(Context context)
	    public static void onActivityStop(Context context)
	    public static void onActivityDestroy(Context context)
	    public static void onActivityNewIntent(Context context, Intent intent)

使用方式：

在入口类中重写上述生命周期对应的方法。

    public static void onApplicationCreate(Context context) {
        if (context instanceof WidgetOneApplication) {
            WidgetOneApplication application = (WidgetOneApplication) context;
        }
    }

    public static void onActivityCreate(Context context) {
        if (context instanceof ACBrowserActivity) {
            ACBrowserActivity activity = (ACBrowserActivity) context;
        }
    }

**注意不可在生命周期方法内做耗时的操作，否则会出现页面卡死的现象。**

- 6.4.添加原生布局
引擎中在页面上添加布局需要通过自定义View和Fragment机制方式。

   - 6.4.1.自定义View方式
一般的原生布局都可以通过该种方式实现。定义一个类继承自线性，相对或其他布局。在其中可直接添加控件，或者引用其他布局文件，并做些交互。具体使用方式可参见插件源码中的`test_addView`方法。

   - 6.4.2.fragment方式
建议开发者在添加一些简单的view的时候使用第一种自定义View方式，但是如果要添加一些比较复杂的布局，或者必须在activity的生命周期中做一些特殊处理的，可用fragment方式，fragment中有activity中相对应的生命周期。

这里需要留意的是，引擎中封装了两种方法添加原生布局，一种是添加到当前窗口，另一种添加到webview上。二者的区别是，前者位置固定，不跟随网页的滚动而滚动，后者跟随网页的滚动而滚动。示例Demo中关于这两种方式的使用方法，开发者可自行选择。

- 6.5.自定义配置
一般第三方sdk在集成的时候都需要做一些特殊的配置，常见的是在AndroidManifest.xml中通过meta-data来配置。为了方便前端开发人员快速配置这些key，插件需要在开发过程中定义相关配置。具体配置方法如下：

   - 6.5.1.插件配置
以高德地图corGaodeMap插件为例，该插件需要在AndroidManifest.xml中配置如下信息：

	<meta-data
	            android:name="com.amap.api.v2.apikey"
	            android:value="您申请的apiKey"/>

其中"您申请的apiKey"则是根据应用的包名或者签名不同而变化的。则插件做如下配置：

	<meta-data
	            android:name="com.amap.api.v2.apikey"
	            android:value="$corGaodeMap_apiKey$"/>

变量是需要`$` 和`$`包裹起来的，变量的命名规则为：插件名称_key值描述。这里为了防止变量名和其他插件冲突并且提高代码可读性，插件开发人员应尽量定义与插件相关的变量名。

# 7.常见问题

   - 7.1.AndroidManifest常见问题
> 表现：

在AndroidManifest下添加的属性不起作用

> 可能原因：

更改或添加了application节点下的相关属性(插件在最后打包时,不会提交application节点,故更改不起作用,开发者不要手动修改application节点)。











