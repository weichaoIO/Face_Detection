
[toc]

---

#<center>**AS 导入 OpenCV 工程之 face-detection**</center>

---

#***TODO***

- [] 

---

#***Reference***

* [AndroidStudio2.2上opencv开发环境搭建](http://blog.csdn.net/ddjjll8877/article/details/52669593 "http://blog.csdn.net/ddjjll8877/article/details/52669593")

* [AS2.2使用CMake方式进行JNI/NDK开发](http://www.jianshu.com/p/cb3064450688 "http://www.jianshu.com/p/cb3064450688")

* [AndroidStudio2.2下利用CMake编译方式的NDK opencv开发](http://blog.csdn.net/ddjjll8877/article/details/52670097 "http://blog.csdn.net/ddjjll8877/article/details/52670097")

* [Android Studio 2.2 NDK开发 opencv 人脸识别](http://blog.csdn.net/a992036795/article/details/53200353 "http://blog.csdn.net/a992036795/article/details/53200353")

---

#**准备工作**

（见 [【Eclipse 导入 OpenCV 工程之 face-detection】]( "")）

---

#**导入工程**

##**打开 AS 并创建新工程**

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/01.png)

##**从 Android 视图转为 Project 视图**

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/02.png)

##**添加 NDK 支持**

>File -> Project Structure...

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/03.png)

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/04.png)

##**导入 lib 模块**

>File -> New -> Import Module...

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/05.png)

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/06.png)

###**非正常情况**

####**target 未找到**

#####**Question**

> Error:Failed to find target with hash string 'android-*' in: *

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/07.png)

#####**Why**

未安装 Android 14 的 SDK。

#####**Answer**

换成已安装的版本。

源代码：

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/08.png)

修改为：

    apply plugin: 'com.android.library'
    
    android {
        compileSdkVersion 25
        buildToolsVersion "25.0.2"
    
        defaultConfig {
            minSdkVersion 18
            targetSdkVersion 25
        }
    
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.txt'
            }
        }
    }

重新编译。

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/09.png)

##**添加 app 模块对 lib 模块的引用**

>File -> Project Structure...

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/10.png)

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/11.png)

选择 lib。

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/12.png)

##**添加 app 模块的 JNI lib**

###**新建 jniLibs 目录**

>右键 main -> New -> Directory

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/13.png)

###**复制 JNI lib 文件到 jniLibs**

JNI lib 文件所在目录：D:\OpenCV-3.2.0-android-sdk\sdk\native\libs

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/14.png)

###**配置 JNI lib**

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/15.png)

修改后，代码为：

    apply plugin: 'com.android.application'
    
    android {
        compileSdkVersion 25
        buildToolsVersion "25.0.2"
        defaultConfig {
            applicationId "io.weichao.face_detection"
            minSdkVersion 18
            targetSdkVersion 25
            versionCode 1
            versionName "1.0"
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
            externalNativeBuild {
                cmake {
                    cppFlags "-std=c++11 -frtti -fexceptions"
                    abiFilters 'x86', 'x86_64', 'armeabi', 'armeabi-v7a', 'arm64-v8a', 'mips', 'mips64'
                }
            }
        }
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
        externalNativeBuild {
            cmake {
                path "CMakeLists.txt"
            }
        }
        sourceSets {
            main {
                jniLibs.srcDirs = ['D:\\android_studio_workspace\\Face_Detection\\app\\src\\main\\jniLibs']
            }
        }
    }
    
    dependencies {
        compile fileTree(include: ['*.jar'], dir: 'libs')
        androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
            exclude group: 'com.android.support', module: 'support-annotations'
        })
        compile 'com.android.support:appcompat-v7:25.2.0'
        compile 'com.android.support.constraint:constraint-layout:1.0.1'
        testCompile 'junit:junit:4.12'
        compile project(':openCVLibrary320')
    }

###**重新编译**

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/16.png)

##**复制 face-detection 工程的资源文件到 app 模块**

资源文件所在目录：D:\OpenCV-3.2.0-android-sdk\samples\face-detection\res

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/17.png)

##**删除 app 模块 java 文件，复制 face-detection 工程的源代码文件到 app 模块**

源代码文件所在目录：D:\OpenCV-3.2.0-android-sdk\samples\face-detection\src

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/18.png)

##**替换 AndroidManifest.xml**

AndroidManifest.xml 文件路径：D:\OpenCV-3.2.0-android-sdk\samples\face-detection\AndroidManifest.xml

##**删除 app 模块 C/C++ 文件，复制 face-detection 工程的 C/C++ 文件到 app 模块**

C/C++ 文件所在目录：D:\OpenCV-3.2.0-android-sdk\samples\face-detection\jni

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/19.png)

##**修改 CMakeLists.txt**

    #CMake版本信息
    cmake_minimum_required(VERSION 3.4.1)
    
    # 支持-std=gnu++11
    set(CMAKE_VERBOSE_MAKEFILE on)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
    
    # 工程路径
    set(pathToProject D:/android_studio_workspace/Face_Detection)
    # OpenCV-android-sdk路径
    set(pathToOpenCv D:/OpenCV-3.2.0-android-sdk)
    
    # 配置加载native依赖
    include_directories(${pathToOpenCv}/sdk/native/jni/include)
    
    # 添加待编译的cpp文件
    add_library(DetectionBasedTracker_jni SHARED ${pathToProject}/app/src/main/cpp/DetectionBasedTracker_jni.h ${pathToProject}/app/src/main/cpp/DetectionBasedTracker_jni.cpp)
    
    # 动态方式加载
    add_library(lib_opencv SHARED IMPORTED)
    # 引入libopencv_java3.so文件
    set_target_properties(lib_opencv PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libopencv_java3.so)
    
    target_link_libraries(DetectionBasedTracker_jni lib_opencv log)

###**非正常情况**

####**无法使用导入的包**

#####**Question**

>Unused import statement

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/20.png)

#####**Why**

* [C语言中<>和“”有什么区别](https://zhidao.baidu.com/question/488136758.html "https://zhidao.baidu.com/question/488136758.html")

`<>`是在系统默认的目录里找头文件，`""`是在和你的当前代码文件相同的目录里找头文件。源代码不严谨，所以此处找不到文件。

#####**Question**

修改代码`#include <DetectionBasedTracker_jni.h>`为`#include "DetectionBasedTracker_jni.h"`

####**函数未定义**

#####**Question**

>undefined reference to `__android_log_print'

![](http://github.com/weichao66666/Face_Detection/raw/master/README.md-images/21.png)

#####**Why**

* [undefined reference to `__android_log_print'](http://stackoverflow.com/questions/4455941/undefined-reference-to-android-log-print "http://stackoverflow.com/questions/4455941/undefined-reference-to-android-log-print")

OpenCV SDK 中的 liblog.so 未导入。

#####**Answer**

    target_link_libraries(log)

---

#**测试**

运行成功。

（结果与[【Eclipse 导入 OpenCV 工程之 face-detection】]( "")一致）

---









