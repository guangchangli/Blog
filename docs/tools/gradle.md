# gradle

## 配置

### ~/.bash_profile

```
GRADLE_HOME=/opt/gradle-6.6
export GRADLE_HOME
export PATH=$PATH:$GRADLE_HOME/bin
```

### ~/.gradle/init.gradle

```
allprojects{
    repositories {
        def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }
        maven {
            url REPOSITORY_URL
        }
    }
}
```

### build.gradle

```
allProjects {
  repositories {
    maven {
      url 'https://maven.aliyun.com/repository/public/'
    }
    maven {
      url 'https://maven.aliyun.com/repository/spring/'
    }
    mavenLocal()
    mavenCentral()
  }
}
```

## 加速

### gradle.properties 

``~/.gradle/gradle.properties && project``

```
#开启并行编译
org.gradle.parallel=true
#开启守护进程 通过开启守护进程，下一次构建的时候，将会连接这个守护进程进行构建，而不是重新fork一个gradle构建进程
org.gradle.daemon=true
#启用新的孵化模式，选择性配置
org.gradle.configureondemand=true
#开启 Gradle 缓存
org.gradle.caching = true
#设置后台进程jvm参数
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
```

