# 常见的项目用法

# 查看依赖关系

方式1：`gradlew :app:dependencies > log.txt`

时间会久一点，在根目录会输出一个log.txt，内容大致是这个样子,缩进表示依赖

```shell
DebugAndroidTestCompileClasspath - Resolved configuration for compilation for variant: casarteDebugAndroidTest
+--- androidx.multidex:multidex:2.0.1
+--- androidx.databinding:databinding-common:3.5.1
+--- androidx.databinding:databinding-runtime:3.5.1
|    +--- androidx.lifecycle:lifecycle-runtime:2.0.0 -> 2.3.1
|    |    +--- androidx.lifecycle:lifecycle-common:2.3.1
|    |    |    \--- androidx.annotation:annotation:1.1.0 -> 1.2.0
|    |    +--- androidx.arch.core:core-common:2.1.0
|    |    |    \--- androidx.annotation:annotation:1.1.0 -> 1.2.0
|    |    \--- androidx.annotation:annotation:1.1.0 -> 1.2.0
|    +--- androidx.collection:collection:1.0.0 -> 1.1.0
|    |    \--- androidx.annotation:annotation:1.1.0 -> 1.2.0
|    \--- androidx.databinding:databinding-common:3.5.1
+--- androidx.databinding:databinding-adapters:3.5.1
|    +--- androidx.databinding:databinding-common:3.5.1
|    \--- androidx.databinding:databinding-runtime:3.5.1 (*)
+--- androidx.multidex:multidex:{strictly 2.0.1} -> 2.0.1 (c)
+--- androidx.databinding:databinding-common:{strictly 3.5.1} -> 3.5.1 (c)
+--- androidx.databinding:databinding-runtime:{strictly 3.5.1} -> 3.5.1 (c)
+--- androidx.databinding:databinding-adapters:{strictly 3.5.1} -> 3.5.1 (c)
+--- project :qq_library
|    +--- androidx.databinding:databinding-common:3.5.1
|    +--- androidx.databinding:databinding-runtime:3.5.1 (*)
|    \--- androidx.databinding:databinding-adapters:3.5.1 (*)
+--- androidx.lifecycle:lifecycle-runtime:{strictly 2.3.1} -> 2.3.1 (c)
+--- androidx.collection:collection:{strictly 1.1.0} -> 1.1.0 (c)
+--- androidx.lifecycle:lifecycle-common:{strictly 2.3.1} -> 2.3.1 (c)
+--- androidx.arch.core:core-common:{strictly 2.1.0} -> 2.1.0 (c)
\--- androidx.annotation:annotation:{strictly 1.2.0} -> 1.2.0 (c)
```

方式2：

1. 依次选择 **View > Tool Windows > Gradle**（或点击工具窗口栏中的 **Gradle** 图标 ![img](https://developer.android.google.cn/static/studio/images/buttons/window-gradle.png)）。
2. 依次展开 **AppName > Tasks > android**，然后双击 **androidDependencies**



# 查看SourceSets

1. 依次选择 **View > Tool Windows > Gradle**（或点击工具窗口栏中的 **Gradle** 图标 ![img](https://developer.android.google.cn/static/studio/images/buttons/window-gradle.png)）。
2. 依次展开 **AppName > Tasks > android**，然后双击 `sourceSets`

![img](https://i0.wp.com/upload-images.jianshu.io/upload_images/689792-996835e75fd0c2dd.png)

可以看到打印的结果,除了`main`还包括其他变体

```shel
main
----
Compile configuration: compile
build.gradle name: android.sourceSets.main
Java sources: [module-shop-special-offer\src\main\java]
Manifest file: module-shop-special-offer\src\main\module\AndroidManifest.xml
Android resources: [module-shop-special-offer\src\main\res]
Assets: [module-shop-special-offer\src\main\assets]
AIDL sources: [module-shop-special-offer\src\main\aidl]
RenderScript sources: [module-shop-special-offer\src\main\rs]
JNI sources: [module-shop-special-offer\src\main\jni]
JNI libraries: [module-shop-special-offer\libs]
Java-style resources: [module-shop-special-offer\src\main\resources]

free
----
Compile configuration: freeCompile
build.gradle name: android.sourceSets.free
Java sources: [module-shop-special-offer\src\free\java]
Manifest file: module-shop-special-offer\src\free\AndroidManifest.xml
Android resources: [module-shop-special-offer\src\free\res]
Assets: [module-shop-special-offer\src\free\assets]
AIDL sources: [module-shop-special-offer\src\free\aidl]
RenderScript sources: [module-shop-special-offer\src\free\rs]
JNI sources: [module-shop-special-offer\src\free\jni]
JNI libraries: [module-shop-special-offer\src\free\jniLibs]
Java-style resources: [module-shop-special-offer\src\free\resources]

freeRelease
-----------
Compile configuration: freeReleaseCompile
build.gradle name: android.sourceSets.freeRelease
Java sources: [module-shop-special-offer\src\freeRelease\java]
Manifest file: module-shop-special-offer\src\freeRelease\AndroidManifest.xml
Android resources: [module-shop-special-offer\src\freeRelease\res]
Assets: [module-shop-special-offer\src\freeRelease\assets]
AIDL sources: [module-shop-special-offer\src\freeRelease\aidl]
RenderScript sources: [module-shop-special-offer\src\freeRelease\rs]
JNI sources: [module-shop-special-offer\src\freeRelease\jni]
JNI libraries: [module-shop-special-offer\src\freeRelease\jniLibs]
Java-style resources: [module-shop-special-offer\src\freeRelease\resources]

......


```

# 配置SourceSets

## 配置文件路径

语法:

`配置项名.srcDirs = ['路径1','路径2'......]`

`配置项名.srcDirs  '路径1','路径2'`



**问题：**配置项都有哪些？可以点击链接查看这里[项目概览  |Android 项目视图](https://developer.android.google.cn/studio/projects#modules)

**问题：**路径如何定义？相对路径还是绝对路径？相对于谁？

> 此路径都是相对于module/build.gralde位置的相对路径
>
> 假设有这么一个层级目录，
>
> shop
>
> -build.gradle
>
> -src
>
> --main/
>
> ---aidl/
>
> ---res/
>
> ---java/
>
> -vip
>
> --aidl/
>
> --main/
>
> ---res/
>
> ---java/

如上文处于`shop/build.gradle`中的`sourceSets`部分，应该是这么写的

```groovy
manifest.srcFile 'src/main/module/AndroidManifest.xml'
res.srcDirs  'src/main/res'
// 或
res.srcDirs = ['src/main/res','vip/main/res']

java.srcDirs 'vip/main/java'
// 或
java.srcDirs = ['src/main/java','vip/main/java']


jniLibs.srcDirs 'libs'
// 或
jniLibs.srcDirs = ['libs']

// 配置多个路径
aidl.srcDirs 'vip/main/aidl','src/aidl'
```

我们可以通过**AppName > Tasks > android**，然后双击 `sourceSets`,查看我们配置后的路径是否生效

比如我们修改了`main`的`sourceSets`

```groovy
      main {
            jniLibs.srcDirs = ['libs']
            if (isComponent.toBoolean()) {
                // 是组件，独立打包apk，读取特定的xml
                manifest.srcFile 'src/main/module/AndroidManifest.xml'
            } else {
                // 不是组件，合并到宿主apk中，读取默认的xml
                manifest.srcFile 'src/main/AndroidManifest.xml'
                java {
                    exclude 'debug/**'
                }
            }
            // 配置单个目录
//            rest.srcDir 'src/main/res'
            // 配置多个目录使用srcDIrs
            res.srcDirs  'src/main/res'
            java.srcDirs = ['src/main/java','vip/main/java']
            assets.srcDirs  'src/main/assets','vip/main/assets'
            aidl.srcDirs 'vip/main/aidl','src/aidl'
        }
```

查看命令行输出：

可以看到`res`、`java`、`assets`、`aidl`路径确实相比之前的默认路径增加了

```powershell
main
----
Compile configuration: compile
build.gradle name: android.sourceSets.main
Java sources: [module-shop-special-offer\src\main\java, module-shop-special-offer\vip\main\java]
Manifest file: module-shop-special-offer\src\main\module\AndroidManifest.xml
Android resources: [module-shop-special-offer\src\main\res]
Assets: [module-shop-special-offer\src\main\assets, module-shop-special-offer\vip\main\assets]
AIDL sources: [module-shop-special-offer\src\main\aidl, module-shop-special-offer\vip\main\aidl, module-shop-special-offer\src\aidl]
RenderScript sources: [module-shop-special-offer\src\main\rs]
JNI sources: [module-shop-special-offer\src\main\jni]
JNI libraries: [module-shop-special-offer\libs]
Java-style resources: [module-shop-special-offer\src\main\resources]
```

## 在变体分类下创建文件

创建`变体/Java`目录

1. 右键点击 `src` 目录，然后依次选择 **New** > **Directory**。
2. 从 **Gradle Source Sets** 下选择 **变体名称/java**。
3. 按 Enter 键。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20220722140937802.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-创建变体sourceSet/java目录
  	</div>
</center>


<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20220722141205088.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-选择变体sourceSet
  	</div>
</center>





创建`xml`资源文件

1. 右键点击 `src` 目录，然后依次选择 **New** > **XML** > **Values XML File**。
2. 输入 XML 文件的名称或保留默认名称。
3. 从 **Target Source Set** 旁边的下拉菜单中，选择 **debug**。
4. 点击 **Finish**。


<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20220722141340528.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-创建xml文件并选择sourceSet目录
  	</div>
</center>


## 进阶配置

文档还提供了更详细的配置，针对于变体、build类型、产品变种各自的[配置方式](https://developer.android.google.cn/static/studio/build/build-variants?hl=zh-cn#sourceset-build)；并且定义了[合并顺序](https://developer.android.google.cn/static/studio/build?hl=zh-cn#sourcesets)，了解这一点对于合并不同的srouceSets很有帮助，尤其是当类名、资源名冲突的时候。

>  合并顺序是：build 变体 > build 类型 > 产品变种 > 主源代码集 > 库依赖项

# 定义常量

## **常量用途**

定义全局依赖库的版本号、apk的调试配置、开发配置

## **常量示例**

Demo参考我的开源项目

### 编写文件

在根工程下建立config.gralde文件

> 注意是必须以ext开头

```groovy
ext {
    /**
     * apk相关参数
     */
    apk = [
            apkAlias: "default-name",
            keyStore: "../test.jks",
            keyAlias: "test",
            storePwd: "123123"
    ]
    androidinfo = [
            compileSdkVersion: 28,
            buildToolsVersion: "28.0.0",
            applicationId    : "com.fly.tour",
            minSdkVersion    : 15,
            targetSdkVersion : 28,
            versionCode      : getCurrentCode().toInteger(),
            versionName      : getCurrentName(),
    ]

    versions = [
            "support-version" : "28.0.0",
            "junit-version"   : "4.12",
            "app-version-name": "20210607",
            "app-version-code": 20210607,

    ]

    support = [
            "constraint-layout"       : "2.0.4",
            'support-v4'              : "com.android.support:support-v4:${versions["support-version"]}",
            'appcompat-v7'            : "com.android.support:appcompat-v7:${versions["support-version"]}",
            'recyclerview-v7'         : "com.android.support:recyclerview-v7:${versions["support-version"]}",
            'support-v13'             : "com.android.support:support-v13:${versions["support-version"]}",
            'support-fragment'        : "com.android.support:support-fragment:${versions["support-version"]}",
            'design'                  : "com.android.support:design:${versions["support-version"]}",
            'animated-vector-drawable': "com.android.support:animated-vector-drawable:${versions["support-version"]}",
            'junit'                   : "junit:junit:${versions["junit-version"]}",
    ]


    jetpack = [
            'appcompat'       : "androidx.appcompat:appcompat:1.1.0",
            'material'        : "com.google.android.material:material:1.1.0",
            'constraintlayout': "androidx.constraintlayout:constraintlayout:1.1.3"
    ]
    dependencies = [ //rxjava
                     "rxjava"                : "io.reactivex.rxjava2:rxjava:2.2.3",
                     "rxandroid"             : "io.reactivex.rxjava2:rxandroid:2.1.0",
                     //rx系列与View生命周期同步
                     "rxlifecycle"           : "com.trello.rxlifecycle2:rxlifecycle:2.2.2",
                     "rxlifecycle-components": "com.trello.rxlifecycle2:rxlifecycle-components:2.2.2",
                     //rxbinding
                     "rxbinding"             : "com.jakewharton.rxbinding2:rxbinding:2.1.1",
                     //rx 6.0权限请求
                     "rxpermissions"         : "com.github.tbruyelle:rxpermissions:0.10.2",
                     //network
                     "okhttp"                : "com.squareup.okhttp3:okhttp:3.10.0",
                     "retrofit"              : "com.squareup.retrofit2:retrofit:2.4.0",
                     "converter-gson"        : "com.squareup.retrofit2:converter-gson:2.4.0",
                     "adapter-rxjava"        : "com.squareup.retrofit2:adapter-rxjava2:2.4.0",
                     "logging-interceptor"   : "com.squareup.okhttp3:logging-interceptor:3.9.1",

                     //glide图片加载
                     "glide"                 : "com.github.bumptech.glide:glide:4.8.0",
                     "glide-compiler"        : "com.github.bumptech.glide:compiler:4.8.0",
                     //json解析
                     "gson"                  : "com.google.code.gson:gson:2.8.5",

                     //Google AAC
                     "lifecycle-extensions"  : "android.arch.lifecycle:extensions:1.1.1",
                     "lifecycle-compiler"    : "android.arch.lifecycle:compiler:1.1.1",
                     //阿里路由框架
                     "arouter-api"           : "com.alibaba:arouter-api:1.4.1",
                     "arouter-compiler"      : "com.alibaba:arouter-compiler:1.2.2",
                     "eventbus"              : "org.greenrobot:eventbus:3.1.1",
                     "dagger"                : "com.google.dagger:dagger:2.13",
                     "dagger-compiler"       : "com.google.dagger:dagger-compiler:2.13",
                     "stetho"                : "com.facebook.stetho:stetho:1.3.1",
                     "MultiImageSelector"    : "com.github.lovetuzitong:MultiImageSelector:1.2"
    ]
}

def getCurrentCode() {
    return "100" + new Date().format("yyMMdd", TimeZone.getTimeZone("GMT+08:00"))
}

def getCurrentName() {
    return "1.00." + new Date().format("yyMMddhh", TimeZone.getTimeZone("GMT+08:00"))
}
```

该文件的末尾，定义了2个方法动态赋值versionName、versionCode

### 导入变量

在根工程下的build.gradle下使用以下语法导入，这样做的效果是根工程下的任意一个library、module都可以访问

```groovy
apply from: "config.gradle"
```

### 使用变量

使用语法为`rootProject.ext.变量名['key名']`

```groovy
dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    api rootProject.ext.jetpack["appcompat"]
    api rootProject.ext.jetpack["material"]
    api rootProject.ext.jetpack["constraintlayout"]
}
```

# 定义组件化

## 组件化用途

组件化是非常典型的架构设计，它的优点有很多，不在此文一一列举了，下面只说实现方式

## 组件化示例

Demo参考我的开源项目

### 定义变量

在根工程下的gradle.properties文件内可以配置变量

```xml
isComponent=true
```

### 定义组件/build.gralde模板

该模板提供以下功能

1. 根据变量区分module、library，方便开发进行组件化调试、方便组件化打包apk，详见参考`android#sourceSets`部分
2. 统一设置了apk签名参数，详见参考`android#signingConfigs`部分
3. 支持自定义apk的参数，如为shop打包时候，自定义apk的名称前缀，方式是`rootProject.ext.apk['apkAlias'] = "shop_special_offer"`和`signingConfigs#outputFileName`
4. 统一设置了apk的各种参数，如versionCode、versionName、Sdk版本等，详见参考`android#defaultConfig`部分

```groovy
/**
 * 是组件，当做Application
 * 不是组件，当做lib
 */
if (isComponent.toBoolean()) {
    apply plugin: 'com.android.application'
} else {
    apply plugin: 'com.android.library'
}
/**
 * 重写apk的名称前缀
 */
rootProject.ext.apk['apkAlias'] = "shop_special_offer"
android {
    /**
     * 编译sdk配置
     */
    compileSdkVersion rootProject.ext.androidinfo.compileSdkVersion
    /**
     * 每个模块的默认配置
     */
    defaultConfig {
        minSdkVersion rootProject.ext.androidinfo.minSdkVersion
        targetSdkVersion rootProject.ext.androidinfo.targetSdkVersion
        versionCode rootProject.ext.androidinfo.versionCode
        versionName rootProject.ext.androidinfo.versionName

        javaCompileOptions {
            annotationProcessorOptions {
                arguments = [AROUTER_MODULE_NAME: project.getName()]
            }
        }
    }
    flavorDimensions "tier"
    productFlavors {
        baidu {

        }
        free {
            /**
             * 构建变体的应用ID
             */
            applicationIdSuffix ".free"
            dimension "tier"
        }
        vip {
            applicationIdSuffix ".vip"
            dimension "tier"
        }
    }

    /**
     * 定义源代码集默认的文件夹路径
     */
    sourceSets {
        androidTestDebug {
            jniLibs.srcDirs = ['libs']
            if (isComponent.toBoolean()) {
                // 是组件，独立打包apk，读取特定的xml
                manifest.srcFile 'src/main/module/AndroidManifest.xml'
            } else {
                // 不是组件，合并到宿主apk中，读取默认的xml
                manifest.srcFile 'src/main/AndroidManifest.xml'
                java {
                    exclude 'debug/**'
                }
            }
        }
        main {
            jniLibs.srcDirs = ['libs']
            if (isComponent.toBoolean()) {
                // 是组件，独立打包apk，读取特定的xml
                manifest.srcFile 'src/main/module/AndroidManifest.xml'
            } else {
                // 不是组件，合并到宿主apk中，读取默认的xml
                manifest.srcFile 'src/main/AndroidManifest.xml'
                java {
                    exclude 'debug/**'
                }
            }
        }
    }

    buildTypes {
        debug {
            /**
             * 根据构建类型 设置应用ID；如果您希望同一设备上同时具有调试版本和发布版本，这会很有用，因为两个 APK 不能具有相同的应用 ID。
             */
            applicationIdSuffix ".debug"
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    signingConfigs {
        release {
            storeFile file(rootProject.ext.apk.keyStore)
            storePassword rootProject.ext.apk.storePwd
            keyAlias rootProject.ext.apk.keyAlias
            keyPassword rootProject.ext.apk.keyPwd
        }

        debug {
            storeFile file(rootProject.ext.apk.keyStore)
            storePassword rootProject.ext.apk.storePwd
            keyAlias rootProject.ext.apk.keyAlias
            keyPassword rootProject.ext.apk.storePwd
        }


        applicationVariants.all { variant ->
            if (variant.buildType.name.equals('debug')) {
                variant.outputs.all {
                    def releaseApkName = rootProject.ext.apk.apkAlias + "_" + versionName + "_debug.apk"
                    outputFileName = releaseApkName
                }
            } else if (variant.buildType.name.equals('release')) {
                variant.outputs.all {
                    def releaseApkName = rootProject.ext.apk.apkAlias + "_" + versionName + ".apk"
                    outputFileName = releaseApkName
                }
            }
        }
    }

}
```



# 远程代码仓库

## 常用的仓库地址

```gro
  		google()
        jcenter()
        mavenCentral()

        // If you're using a version of Gradle lower than 4.1, you must instead use:
        // maven {
        //     url 'https://maven.google.com'
        // }
        // An alternative URL is 'https://dl.google.com/dl/android/maven2/'
```

国内可能需要配置阿里云maven

| 仓库名称      | 阿里云仓库地址                                    | 源地址                          |
| :------------ | :------------------------------------------------ | :------------------------------ |
| central       | https://maven.aliyun.com/repository/central       | https://repo1.maven.org/maven2/ |
| jcenter       | https://maven.aliyun.com/repository/public        | http://jcenter.bintray.com/     |
| public        | https://maven.aliyun.com/repository/public        | central仓和jcenter仓的聚合仓    |
| google        | https://maven.aliyun.com/repository/google        | https://maven.google.com/       |
| gradle-plugin | https://maven.aliyun.com/repository/gradle-plugin | https://plugins.gradle.org/m2/  |

## 下载顺序说明

代码库的列出顺序决定了Gradle在代码库中搜索各个项目依赖项的顺序，如果在google仓找到了A，那么Gradle只会在google中下载A依赖项

```groovy
allprojects {
    repositories {
        google()
        jcenter()
    }
}
```

## 声明特定的地址

语法为：`仓库名{ url  "地址"}`

```groovy
allprojects {
    repositories {
        maven {
            url "https://repo.example.com/maven2"
        }
        maven {
            url "file://local/repo/"
        }
        ivy {
            url "https://repo.example.com/ivy"
        }
    }
}
```



# 依赖指定位置的库

## 本地library依赖

语法为：`implementation project（':库文件夹名称'）`

- 括号里单引号双引号都可以，注意加冒号
- 库文件夹名称，必须在settings.gradle中include

```groovy
implementation project(':mylibrary')
```

## 本地jar二进制依赖

语法为：先声明fileTree、匹配二进制依赖的正则

- 指向的位置是`module_name/dir`,module是当前application目录，dir是开发传入的值
- 匹配规则是include传入的值

```groovy
implementation fileTree(dir: 'libs', include: ['*.jar'])
implementation files('libs/foo.jar', 'libs/bar.jar')
```

## 远程jar二进制依赖

语法为：`implementation '命名空间组名称:依赖库名称:依赖库版本号@jar'`

@jar可以简写

```groovy
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.0.0:sources@jar'
```

## 远程二进制依赖

> 当使用此语法时，Gradle会根据参数寻找远程代码库

语法为：`implementation group: 命名空间组名称，name:依赖库名称,version:依赖库版本号`

```groovy
implementation group: 'com.example.android', name: 'app-magic', version: '12.3'
```

**简化的语法如下：**

`implementation '命名空间组名称:依赖库名称:依赖库版本号'`

```groovy
implementation 'com.example.android:app-magic:12.3'
```


## 本地aar二进制依赖

本地`aar`依赖语法：

一种是`implementation()`，括号里面带上`name`和`ext`的键值对

一种是`implementation files()`，括号里带`上aar`的相对位置

```groovy
implementation fileTree(dir: 'libs', include: ['*.jar','*.aar'])  
implementation(name: 'aar包名', ext: 'aar')
// 路径是相对于module模块的根路径地址
implementation files('libs/imageutil.aar')
```

**用法：**

第一步：`module/build.gralde中`加入`flatDir`对象,`flatDir`声明了`libs`目录相对于`module`模块位置

```groovy
repositories {
    flatDir {
        dirs 'libs' //this way we can find the .aar file in libs folder
    }
}
```

第二步：`fileTree`配置过滤的正则，加上`.aar`

```groovy
compile fileTree(dir: 'libs', include: ['*.jar','*.aar'])
```

第三步：引入aar

```groovy
compile(name: 'wheelview', ext: 'aar')
implementation(name: 'wheelview', ext: 'aar')
或
implementation files('libs/imageutil.aar')
```



## 远程aar依赖

`aar`的远程依赖写法：

```groovy
implementation fileTree(dir: 'libs', include: ['*.jar','*.aar'])  
implementation '命名空间名称:库名称:版本号@aar'
```



```groovy
android{
    repositories {
        flatDir {
            dirs 'libs'
        }
    }
}

......

implementation fileTree(dir: 'libs', include: ['*.jar','*.aar'])
implementation (name: 'barcode_scanner_library_v2.3.2.0', ext: 'aar') 
implementation 'com.android.support:viewpager:28.0.0@aar'
```



## 本地so依赖

第一步：so文件放在`module/libs`目录下

第二步：将`jniLibs.srcDirs`的赋值为so文件位于module中的相对位置：`libs`

```groovy
sourceSets {

    main {

    	jniLibs.srcDirs = ['libs']

    }

}
```



# 依赖方式配置

## 基础配置项

| 配置项         | 解释                               | 依赖传递 | 是否构建至编译类路径 | 是否构建至apk<br>运行时能使用 |
| -------------- | ---------------------------------- | -------- | -------------------- | ----------------------------- |
| implementation | 避免了依赖传递                     | 否       |                      | 是                            |
| api            | 就是gradle插件3.0版本提供的compile | 是       | 是                   | 是                            |
| compile        | 就是gradle插件2.0版本提供的api     | 是       | 是                   | 是                            |
| apk            |                                    |          | 否                   | 是                            |
| compile        |                                    | 是       | 是                   | 是                            |
| provided       |                                    | 否       | 是                   | 否                            |
| compileOnly    |                                    |          | 是                   | 否                            |
| runtimeOnly    |                                    |          | 否                   | 是                            |

> 还有其他的配置项可以参考文档[添加构建依赖项  | Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/studio/build/dependencies#native_dependencies)

## 排除依赖项

语法：`excluede group: '命名空间名称',module:'模块名称'`

示例：

```groovy
dependencies {
    implementation('some-library') {
        exclude group: 'com.example.imgtools', module: 'native'
    }
}
```



# 常见的依赖项错误

## 重复错误

错误提示: `Program type already present com.example.MyClass`

错误原因通常是下面2种情况之一：

1. 宿主App依赖库A，库B；但库A已经依赖了库B

   解决：取消宿主App对B的依赖

2.  宿主App依赖库A（本地依赖）和库B（远程依赖），库A和库B是同一个依赖项

   解决：宿主App移除任意一个的依赖