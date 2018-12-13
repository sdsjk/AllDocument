### Android  9  http适配

##### 在Android 9上，为了用户 的隐私禁止使用http去请求，需要Https去网络请求，怎么是原来的http还能使用呢？

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
