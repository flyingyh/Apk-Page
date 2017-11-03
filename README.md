# Apk-Page
友盟的多渠道打包
多渠道打包
 1.原理：为我们每个平台的apk指定一个唯一的标识符
 2.使用友盟步骤：
 A.下载SDK并集成，添加对友盟库的依赖
 B.在manifest.xml中配置渠道号和Appkey，以及我们的渠道占位符
 C.在应用module中的builder.gradle中编写多渠道脚本，动态的去替换我们占位符的	   value
 D.试过：（在控制台使用./gradlew assebmleRelease去自动的打出所有渠道的包
   assebmleDebug assebmlewandoujiaRelease），出现了问题，没解决。
 直接view----》Tool windows -----》gradle-----》项目中的build

Studio具体步骤：

Gradle中：
compile 'com.umeng.analytics:analytics:6.1.2'

配置文件manifest中：
<!-- 必须的权限 -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />

<meta-data android:value="59fae39eaed179448f000039" android:name="UMENG_APPKEY"/><!--友盟Appkey-->
<meta-data android:value="${UMENG_CHANNEL_VALUE}" android:name="UMENG_CHANNEL"/><!--渠道号-->

Builder.gradle中：
defaultConfig下
{
multiDexEnabled true //突破应用方法数65535的一个限制
manifestPlaceholders = [UMENG_CHANNEL_CALUE:"umeng"]
}

//添加我们的签名配置文件
    signingConfigs{
        debug{}

        //为我们的release添加签名配置
        release{
            storeFile file("E:/flyingyh.jks")//你的jks绝对路径
            storePassword "****"//密码
            keyAlias "******" //用户名
            keyPassword "******"//自己的密码
        }
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

            signingConfig signingConfigs.release
        }
    }

    productFlavors{
        xiaomi{
//            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "xiaomi"]
//            resValue "string","app_name","xiaomi_app"
        }
        wandoujia{
//            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "wandoujia"]
//            resValue "string","app_name","wandoujia_app"
        }
    }

    productFlavors.all{
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
    }
