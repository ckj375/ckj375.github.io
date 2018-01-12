---
title: Gradle中productFlavors使用详解
date: 2018-01-12 15:41:04
tags: [Gradle,productFlavors]
categories: Android
---

最早接触productFlavors是开发第三方Android OS的时候，由于要给不同的厂商做定制，并且适配不同的硬件平台，所以发版本的时候，经常要切换项目分支，然后逐个编译APK。然而有了productFlavors就可以简化这一步操作，不用切换项目分支就可以编译调试不同项目版本的APK，并且可以快速打包所有项目版本的APK。

下面来看下具体用法，在build.gradle（Module:app）中添加productFlavors和flavorDimensions，其中productFlavors用来定义不同特性的产品，flavorDimensions用来定义维度（至少需要定义1个维度）。<!-- more -->举个例子：APK要适配不同厂商的不同机型，现在有2个厂商需要适配，于此同时，每个厂商分别有高配，中配，低配3个机型，则定义如下：
```
flavorDimensions "cooperator", "model"
productFlavors {
    cooperatorA {
        flavorDimension "cooperator"
    }
    cooperatorB {
        flavorDimension "cooperator"
    }
    modelA {
        flavorDimension "model"
    }
    modelB {
        flavorDimension "model"
    }
    modelC {
        flavorDimension "model"
    }
}
```
cooperator表示厂商维度，model表示机型维度，最终编译对应12个Build Variant，分别是
```
cooperatorAmodelADebug
cooperatorAmodelARelease
cooperatorAmodelBDebug
cooperatorAmodelBRelease
cooperatorAmodelCDebug
cooperatorAmodelCRelease
cooperatorBmodelADebug
cooperatorBmodelARelease
cooperatorBmodelBDebug
cooperatorBmodelBRelease
cooperatorBmodelCDebug
cooperatorBmodelCRelease
```

#### 配置属性
如果要配置不同的包名，版本号等，可直接在对应的产品定义中指定相应的属性值；如果要配置渠道号可以通过${name}的形式在AndroidManifest.xml中插入一个占位符，编译的时候会替换为真实值。
```
flavorDimensions "cooperator"
    productFlavors {
        cooperatorA {
            applicationId "com.ckj.myapplication1"
            manifestPlaceholders = [UMENG_CHANNEL_VALUE:"channel1"]
            versionCode 1
            versionName "1.0"
            dimension "cooperator"
        }
        cooperatorB {
            applicationId "com.ckj.myapplication2"
            manifestPlaceholders = [UMENG_CHANNEL_VALUE:"channel2"]
            versionCode 2
            versionName "1.0.1"
            dimension "cooperator"
        }
    }
```
在AndroidManifest.xml中添加渠道变量
```
<application
    ...>
    ...
    <meta-data
        android:name="UMENG_CHANNEL"
        android:value="${UMENG_CHANNEL_VALUE}" />
</application>
```

#### 配置常量
如果要配置A版本支持自动更新功能，B版本不支持自动更新功能，
格式为：buildConfigField "boolean", "SUPPORT_AUTO_UPDATE_FEATURE", "true"
三个字段分别表示:自定义字段类型,自定义字段名,自定义字段值
```
flavorDimensions "cooperator"
    productFlavors {
        cooperatorA {
            buildConfigField "boolean", "SUPPORT_AUTO_UPDATE_FEATURE", "true"
            dimension "cooperator"
        }
        cooperatorB {
            buildConfigField "boolean", "SUPPORT_AUTO_UPDATE_FEATURE", "false"
            dimension "cooperator"
        }
    }
```
添加完后同步下Gradle就可以在编译生成目录(app/build/generated/source/buildConfig)下的BuildConfig.java中看到该自定义字段。在需用用到该自定义字段的代码处直接调用BuildConfig.SUPPORT_AUTO_UPDATE_FEATURE即可获取自定义字段值。
```
if(isSupportAutoUpdate == BuildConfig.SUPPORT_AUTO_UPDATE_FEATURE){
    ...
}
```

#### 配置代码目录
在module目录下创建"src/cooperatorA/java"和"src/cooperatorB/java"目录，与原有的"src/main/java"目录对称,在build.gradle中，通过sourceSets指定cooperatorA和cooperatorB的源码目录。在实际编译过程中，根据选择的Build Variant到指定的源码目录查找源码文件，并且将main目录下的源码和指定目录下的源码进行合并（注："src/cooperatorA/java"和"src/cooperatorB/java"目录只存放差异类，共有的类依然是在src/main/java目录下）
```
flavorDimensions "cooperator"
productFlavors {
    cooperatorA {
        dimension "cooperator"
    }
    cooperatorB {
        dimension "cooperator"
    }
}
sourceSets {
    cooperatorA {
        java.srcDirs = ['src/cooperatorA/java']
    }
    cooperatorB {
        java.srcDirs = ['src/cooperatorB/java']
    }
}
```

#### 配置资源文件
例如要配置A版本应用名为Name_A,则在src目录下创建cooperatorA目录，并添加如下字符串资源：src/cooperatorA/res/values/strings.xml
```
<resources>
    <string name="app_name">Name_A</string>
</resources>
```
"src/cooperatorA"目录默认为cooperatorA flavor的dataSet，运行gradle assemblecooperatorA命令即可生成应用名为Name_A的应用。
```
flavorDimensions "cooperator"
productFlavors {
    cooperatorA {
        dimension "cooperator"
    }
    cooperatorB {
        dimension "cooperator"
    }
}
```

#### 配置不同库
APK运行环境不同，使用的库也会不一样，通常会把所有的库都添加进来，然后跟据运行环境调用不同的库，通过productFlavors我们可以针对不同的product编译指定的库，无需编译无用库。
```
flavorDimensions "cooperator"
productFlavors {
    cooperatorA {
        dimension "cooperator"
    }
    cooperatorB {
        dimension "cooperator"
    }
}
dependencies {
    cooperatorACompile 'com.squareup.retrofit2:retrofit:2.2.0'
}
```
