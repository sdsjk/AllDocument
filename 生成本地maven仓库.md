### 1 .�����ع�������maven �ֿ�

``` java

// ���������ؿ� ----begin-----------------------------------------
apply plugin: 'maven'
uploadArchives{
    repositories {
        mavenDeployer {
            repository(url: uri('../repository'))
            pom.groupId = "com.testutils"
            pom.artifactId = "myutils"
            pom.version = "1.0.2"
        }
    }
}
// ���������ؿ� ---- end -----------------------------------------

```

�����ش�������maven�ֿ�

### 2. �����ص�arr����

``` java
enter code// ���������ؿ� ----begin-----------------------------------------
apply plugin: 'maven'
def coreAarFile = file('build/outputs/aar/Engine-system-release.aar')
artifacts {
    archives coreAarFile
}
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: uri('repository'))
            pom.groupId = "org.appcan"
            pom.artifactId = "engine"
            pom.version = "4.3.1"
        }
    }
}
// ���������ؿ� ---- end ----------------------------------------- here
```

Զ��������ʽ

``` javascript
repositories {
    flatDir {
        dirs 'libs'
    }
    maven {
        url 'F:\\mvn-repo'  ��Ӧ�Ĳֿ��ַ
    }
}

```
�����gradle��Զ�� ��ʽΪ groupId ��artifactId��version