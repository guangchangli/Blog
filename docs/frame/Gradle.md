# Gradle

```
~/.gradle/wrapper/dists 							下载的 src
~/.gradle/caches/modules-2/files-2.1	下载的 依赖
~/.gradle/wrapper/dists/gradle-6.4.1-bin/33549z180oh3o1s64iwo25l48/gradle-6.4.1/init.d 启动脚本

```

```
依赖 scope
maven   complie(默认)、provided、runtime、test、system、import
gradle  compile runtime testCompile testRuntime
```

## 加速

```groovy
~/.gradle touch init.gradle

buildscript {
    repositories {
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        }
    }
}
```

```groovy
in project
allprojects {
    repositories {
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        }
    }
}
```

```
~/.gradle touch gradle.properties
org.gradle.daemon=true
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
org.gradle.parallel=true
org.gradle.configureondemand=true
//支持中文文件  
systemProp.file.encoding=UTF-8  
```

```
in project
gradle/wrapper gradle-wrapper.properties
distributionUrl=file\:/Users/lgc/.gradle/wrapper/dists/gradle-5.2.1-bin/9lc4nzslqh3ep7ml2tp68fk8s/gradle-5.2.1-bin.zip
```



## 加载本地依赖

```groovy
dependencies {
    compile fileTree(dir: 'src/main/libs', includes: ['*.jar'])
}
```

## build.gradle

```groovy
//gradle 脚本自身需要的环境，预先加载
buildscript {
    ext {
        springBootVersion = '2.3.2.RELEASE'
    }
    repositories {
        //mavenCentral()
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
    }
    // 依赖关系
    dependencies {
        // classpath 声明说明了在执行其余的脚本时，ClassLoader 可以使用这些依赖项
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}
plugins {
    id 'java'
    id 'idea'
//    id 'war'
}
//定义目录结构
sourceSets {
    main {
        java {
            srcDirs = ['src/mian/java']
        }
        resources {
            srcDirs = ['src/mian/resources']
        }
    }
}
/**
 * 引入插件
 * 可以引入插件文件或者网络文件插件
 * apply from:''
 */
apply plugin: 'idea'


group 'com.aladdin'
version '1.0-SNAPSHOT'
/**
 * jdk 编译版本
 */
sourceCompatibility = 1.8

repositories {
    maven {
        url 'http://maven.aliyun.com/nexus/content/groups/public/'
    }
    mavenLocal()
    mavenCentral()
}
configurations.all {
//    resolutionStrategy{
//        // 修改gradle不处理版本冲突
//        failOnVersionConflict()
//    }
    /**
     * 强制指定版本
     */
    resolutionStrategy {
        force 'org.slf4j:slf4j-api:1.7.24'
    }
}

dependencies {
    /**
     * + 动态声明版本
     */
    compile('org.springframework.boot:spring-boot-starter-web:2.3.2.RELEASE')
    compile 'ch.qos.logback:logback-examples:1.+'
    compile 'org.hibernate:hibernate-core:3.6.3.Final'
}
```



## 依赖冲突

``gradle 自己处理默认使用最高版本``

- 查看依赖分析 【 tasks-help-dependencies】
- 排除依赖
- 强制版本冲突

### 修改默认策略

```groovy
configurations.all{
  resolutionStrategy{
      // 修改gradle不处理版本冲突
      failOnVersionConflict()
  }
}
```

### 排除传递性依赖

```groovy
compile('org.hibernate:hibernate-core:3.6.3.Final'){
  // module是坐标里面的name
  exclude group:"org.slf4j" , module:"slf4j-api"
}
```

### 强制使用一个版本

```groovy
configuration.all{
  resolutionStrategy{
      force 'org.slf4j:slf4j-api:1.7.24'
  }
}
```

## 父子工程

```
setting.properties
rootProject.name = 'projects'
include 'model'
include 'services'
include 'web'
```

```groovy
group 'xxx'
version '1.0-SNAPSHOT'

// 所有项目配置
allprojects {
     apply plugin:'java'
     sourceCompatibility = 1.8
}

// 子项目下配置
subprojects {
  repositories {
     mavenCentral()
  }
  dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile "org.apache.logging.log4j:log4j-osgi:2.11.1"
  }
}
```

