### Android  9  http����

##### ��Android 9�ϣ�Ϊ���û� ����˽��ֹʹ��httpȥ������ҪHttpsȥ����������ô��ԭ����http����ʹ���أ�

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
