# Android-JNITest
Android Studio JNI environment.  
Android Studio JNI环境配置。

------

## 介绍

Android Studio + NDK来实现JNI。

什么是NDK与JNI技术？  
NDK：Native Development Kit  

The NDK is a toolset that allows you to implement parts of your app using native-code languages such as C and C++.（谷歌官方文档）

大致意思：NDK是一个工具，可以让你实现你的应用程序使用本地代码的语言，如C和C++的部分。

JNI：Java Native Interface  
它提供了若干的API实现了Java和其他语言的通信（主要是C&C++）。从Java1.1开始，JNI标准成为java平台的一部分，它允许Java代码和其他语言写的代码进行交互。

## 准备工作

* 1.搭建好Android Studio开发环境。
* 2.新建一个Android项目

## Android Studio配置NDK
* 1.如图所示下载LLDB+NDK并安装。  
![第一][1]

* 2.配置安装好的NDK路径。  
![第二][2]

* 3.配置一些快捷方式。  
![第三][3]  
![javah][4]  
![ndk-build三][5]  
![ndk-build clean][6]  
```text
	javah	用于生成头文件
	Program：$JDKPath$/bin/javah
	注意：这个命令我加上了-encoding UTF-8指定编码，你可以改成你工程的编码。
	Parameters：-encoding UTF-8 -d ../jni -jni $FileClass$
	Working directory：$SourcepathEntry$\..\java

	ndk-build	用于构建so包
	注意：MAC/Linux用ndk-build，没有.cmd后缀
	Program：C:\Develop\Android\sdk\ndk-bundle\ndk-build.cmd
	Parameters：什么都不用填
	Working directory：$ModuleFileDir$\src\main

	ndk-build clean	清除so包
	注意：MAC/Linux用ndk-build，没有.cmd后缀
	Program：C:\Develop\Android\sdk\ndk-bundle\ndk-build.cmd
	Parameters：clean
	Working directory：$ModuleFileDir$\src\main
```

## 配置项目
* 在gradle.properties文件中添加  
```xml
	android.useDeprecatedNdk=true
```
* 修改文件目录如下  
![修改文件目录][7] 

* 参考[Android Studio Project Site](http://tools.android.com/tech-docs/new-build-system/gradle-experimental)


* 1.修改根目录下的build.gradle  
```xml
	buildscript {
	    repositories {
		jcenter()
	    }
	    dependencies {
		//	修改build:gradle为build:gradle-experimental
        	classpath "com.android.tools.build:gradle-experimental:0.7.0"
		//        classpath 'com.android.tools.build:gradle:2.1.2'
	    }
	}
	
	allprojects {
	    repositories {
	        jcenter()
	    }
	}
	//添加
	task clean(type: Delete) {
	    delete rootProject.buildDir
	}
```

* 2.修改gradle->wrapper->gradle-wrapper.properties  
```xml
	#Mon Dec 28 10:00:20 PST 2015
	distributionBase=GRADLE_USER_HOME
	distributionPath=wrapper/dists
	zipStoreBase=GRADLE_USER_HOME
	zipStorePath=wrapper/dists
	distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip//修改这里的版本号
```
* gradle-experimental与gradle-wrapper相对应的版本号如下图  
![版本号对比][8] 

* 3.修改app->build.gradle  
```xml
	修改之前的
	apply plugin: 'com.android.application'
	
	android {
	    compileSdkVersion 23
	    buildToolsVersion "23.0.3"
	
	    defaultConfig {
	        applicationId "com.jeanboy.demo.jnitest"
	        minSdkVersion 15
	        targetSdkVersion 23
	        versionCode 1
	        versionName "1.0"
	    }
	    buildTypes {
	        release {
	            minifyEnabled false
	            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	        }
	    }
	}
	
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
	    compile 'com.android.support:appcompat-v7:23.4.0'
	}
```
```xml
	修改之后的
	apply plugin: 'com.android.model.application'//修改
	//apply plugin: 'com.android.application'
	
	model {//修改
	    android {
	        compileSdkVersion 23
	        buildToolsVersion "23.0.3"
	
	        defaultConfig {
	            applicationId "com.jeanboy.demo.jnitest"
	            minSdkVersion.apiLevel 15
	            targetSdkVersion.apiLevel 23
	            versionCode   1
	            versionName   "1.0"
	        }
	
	        ndk {//指定生成的lib，比如此时生成native.so
	            moduleName   "NdkTest"
	        }
	
	        buildTypes {
	            release {
	                minifyEnabled false
	                proguardFiles.add(file("proguard-rules.pro"))//修改
	            }
	        }
	
	    }
	
	}
	
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
	    compile 'com.android.support:appcompat-v7:23.4.0'
	}
```

* 4.创建jni文件夹  
![创建jni文件夹][9]

* 5.创建NdkTest.java  
```java
	public class NdkTest {
	    static {
	        System.loadLibrary("NdkTest");//加载要使用的so文件
	    }
		//生命native方法
	    public static native String getString();
	    public static native int doAdd(int param1,int param2);
	}
```

* 6.生成NdkTest.h并创建NdkTest.cpp实现NdkTest.h中的native方法  
![生成NdkTest.h][10]

NdkTest.h文件内容
```c
	/* DO NOT EDIT THIS FILE - it is machine generated */
	#include <jni.h>
	/* Header for class com_jeanboy_demo_jnitest_NdkTest */
	
	#ifndef _Included_com_jeanboy_demo_jnitest_NdkTest
	#define _Included_com_jeanboy_demo_jnitest_NdkTest
	#ifdef __cplusplus
	extern "C" {
	#endif
	/*
	 * Class:     com_jeanboy_demo_jnitest_NdkTest
	 * Method:    getString
	 * Signature: ()Ljava/lang/String;
	 */
	JNIEXPORT jstring JNICALL Java_com_jeanboy_demo_jnitest_NdkTest_getString
	  (JNIEnv *, jclass);//待实现的native方法
	
	/*
	 * Class:     com_jeanboy_demo_jnitest_NdkTest
	 * Method:    doAdd
	 * Signature: (II)I
	 */
	JNIEXPORT jint JNICALL Java_com_jeanboy_demo_jnitest_NdkTest_doAdd
	  (JNIEnv *, jclass, jint, jint);//待实现的native方法
	
	#ifdef __cplusplus
	}
	#endif
	#endif
```

NdkTest.cpp文件内容
```c
	#include "com_jeanboy_demo_jnitest_NdkTest.h"

	JNIEXPORT jstring JNICALL Java_com_jeanboy_demo_jnitest_NdkTest_getString
	        (JNIEnv *env, jclass type) {//具体实现
	
	    return env->NewStringUTF("hello world!!!");
	}
	
	/*
	 * Class:     com_jeanboy_demo_jnitest_NdkTest
	 * Method:    doAdd
	 * Signature: (II)I
	 */
	JNIEXPORT jint JNICALL Java_com_jeanboy_demo_jnitest_NdkTest_doAdd
	        (JNIEnv *env, jclass type, jint param1, jint param2) {//具体实现
	
	    return param1 + param1;
	}
```


* 7.在jni文件夹下创建Android.mk和Application.mk  

Android.mk文件内容  
```c
	LOCAL_PATH := $(call my-dir)
	include $(CLEAR_VARS)
	
	LOCAL_MODULE := NdkTest//moduleName
	LOCAL_SRC_FILES := NdkTest.cpp//上面创建的NdkTest.cpp
	include $(BUILD_SHARED_LIBRARY)
```

Application.mk文件内容  
```c
	APP_MODULES := NdkTest
	/*这个变量是可选的，如果没有定义，NDK将由在Android.mk中声明的默认的模块编译，并且包含所有的子文件(makefile文件)；
	如果APP_MODULES定义了，它不许是一个空格分隔的模块列表，这个模块名字被定义在Android.mk文件中的LOCAL_MODULE中。
	注意NDK会自动计算模块的依赖*/
	APP_ABI := all//支持所有平台，也可以指定平台空格隔开armeabi armeabi-v7a x86
```

```text
	Android系统目前支持的CPU架构：

	ARMv5，ARMv7 (从2010年起)
	x86 (从2011年起)
	MIPS (从2012年起)
	ARMv8，MIPS64和x86_64 (从2014年起)

	每一个CPU架构对应一个ABI
	CPU架构			ABI
	ARMv5	--->	armeabi
	ARMv7	--->	armeabi-v7a
	x86		--->	x86
	MIPS	--->	mips
	ARMv8	--->	arm64-v8a
	MIPS64	--->	mips64
	x86_64	--->	x86_64

	armeabi：默认选项，将创建以基于ARM* v5TE 的设备为目标的库。 具有这种目标
	的浮点运算使用软件浮点运算。 使用此ABI（二进制接口）创建的二进制代码将可以
	在所有 ARM*设备上运行。所以armeabi通用性很强。但是速度慢

	armeabi-v7a：创建支持基于ARM* v7 的设备的库，并将使用硬件FPU指令。
	armeabi-v7a是针对有浮点运算或高级扩展功能的arm v7 cpu。

	mips：MIPS是世界上很流行的一种RISC处理器。MIPS的意思是“无内部互锁流水级
	的微处理器”(Microprocessor without interlocked piped stages)，其机
	制是尽量利用软件办法避免流水线中的数据相关问题。

	x86：支持基于硬件的浮点运算的IA-32 指令集。x86是可以兼容armeabi平台运行
	的，无论是armeabi-v7a还是armeabi，同时带来的也是性能上的损耗，另外需要
	指出的是，打包出的x86的so，总会比armeabi平台的体积更小。

	总结
	如果项目只包含了 armeabi，那么在所有Android设备都可以运行；
	如果项目只包含了 armeabi-v7a，除armeabi架构的设备外都可以运行； 
	如果项目只包含了 x86，那么armeabi架构和armeabi-v7a的Android设备是无法
	运行的；
	如果同时包含了 armeabi，armeabi-v7a和x86，所有设备都可以运行，程序在运
	行的时候去加载不同平台对应的so，这是较为完美的一种解决方案，同时也会导致
	包变大。
```

* 8.生成so文件  
![生成so文件][11]

* 9.在需要native方法的地方直接调用 
```java
	NdkTest.getString();
	NdkTest.doAdd(5, 12);
```

* 10.运行app试试效果吧

## 关于我

* Mail: jeanboy@foxmail.com

## License

    Copyright 2015 jeanboy

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.




 [1]:https://github.com/freekite/Android-JNITest/blob/master/resources/1.png
 [2]:https://github.com/freekite/Android-JNITest/blob/master/resources/2.png
 [3]:https://github.com/freekite/Android-JNITest/blob/master/resources/3.png
 [4]:https://github.com/freekite/Android-JNITest/blob/master/resources/4.png
 [5]:https://github.com/freekite/Android-JNITest/blob/master/resources/5.png
 [6]:https://github.com/freekite/Android-JNITest/blob/master/resources/6.png
 [7]:https://github.com/freekite/Android-JNITest/blob/master/resources/7.png
 [8]:https://github.com/freekite/Android-JNITest/blob/master/resources/8.png
 [9]:https://github.com/freekite/Android-JNITest/blob/master/resources/9.png
 [10]:https://github.com/freekite/Android-JNITest/blob/master/resources/10.png
 [11]:https://github.com/freekite/Android-JNITest/blob/master/resources/11.png
