### 1 .将本地工程生成maven 仓库

``` java

// 发布到本地库 ----begin-----------------------------------------
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
// 发布到本地库 ---- end -----------------------------------------

```

将本地代码生成maven仓库

### 2. 将本地的arr生成

``` java
enter code// 发布到本地库 ----begin-----------------------------------------
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
// 发布到本地库 ---- end ----------------------------------------- here
```

远程依赖方式

``` javascript
repositories {
    flatDir {
        dirs 'libs'
    }
    maven {
        url 'F:\\mvn-repo'  相应的仓库地址
    }
}

```
最后在gradle中远程 方式为 groupId ：artifactId：version