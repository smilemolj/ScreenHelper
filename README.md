# ScreenHelper

android 屏幕适配的两种方式


## 第一种适配方式 - sw 方式
插件版本：
[ ![Download](https://api.bintray.com/packages/bugyun/maven/screen-plugin/images/download.svg?version=1.0.0) ](https://bintray.com/bugyun/maven/screen-plugin/1.0.0/link)

使用方法

在项目的根 build.gradle 中添加,如果jcenter 仓库找不到项目，那么可以添加我的仓库

```java
buildscript {
    ext.kotlin_version = '1.3.31'
    repositories {
        google()
        jcenter()
        //maven { url "https://dl.bintray.com/bugyun/maven" } //我的仓库,如果 jcenter 能找到，就不要添加
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.0-alpha13'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        // 在此处添加
        classpath 'vip.ruoyun.plugin:screen-plugin:1.0.0'
    }
}
allprojects {
    repositories {
        google()
        jcenter()
        //maven { url "https://dl.bintray.com/bugyun/maven" } //我的仓库,如果 jcenter 能找到，就不要添加
    }
}
```

在要使用插件的的子项目的 build.gradle 中添加

```java
apply plugin: 'vip.ruoyun.screen'

screen {
    smallestWidths 320, 360, 384, 392, 400, 410, 411, 432, 480 //生成的目标屏幕宽度的适配文件
    designSmallestWidth 375 //苹果设计稿750 × 1334   屏幕宽度为 375
    decimalFormat "#.#" //设置保留的小数 ( #.## 保留2位) ( #.# 保留1位)
    log false //是否打印日志
    auto false //是否每次 build 项目的时候自动生成 values-sw[]dp 文件
}
```

如果 auto 设置为 true ,则每次 build 项目的时候自动生成 values-sw[]dp 文件
如果 auto 设置为 false,则可以通过命令行,来生成文件.
```
./gradlew dimensCovert
```
也可以在 gradle命令的 窗口中 点击 dimensCovert 的 task.

![](https://github.com/bugyun/ScreenHelper/blob/master/art/15572467899577.jpg?raw=true)

自动生成的sw 文件

![](https://github.com/bugyun/ScreenHelper/blob/master/art/15572468488661.jpg?raw=true)

生成规则：只会生成 dp 后缀的属性值，根据 values 目录下的 dimens.xml,生成具体的文件。
values 目录下的 dimens.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <dimen name="x20">20dp</dimen>
    <dimen name="x30">20sp</dimen>
</resources>
```
生成的目标文件,values-sw320dp 目录下的 dimens.xml 文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<resources>
    <dimen name="x20">17.1dp</dimen>
</resources>
```

## 第二种适配方式 - 通过代码来实现适配
插件版本：
[ ![Download](https://api.bintray.com/packages/bugyun/maven/screen-helper/images/download.svg?version=1.0.0) ](https://bintray.com/bugyun/maven/screen-helper/1.0.0/link)

在项目的根 build.gradle 中添加 jcenter ,如果jcenter 仓库找不到项目，那么可以添加我的仓库
```java
buildscript {
    ext.kotlin_version = '1.3.31'
    repositories {
        google()
        jcenter()
        //maven { url "https://dl.bintray.com/bugyun/maven" }//我的仓库,如果 jcenter 能找到，就不要添加
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.0-alpha13'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

allprojects {
    repositories {
        google()
        jcenter()
        //maven { url "https://dl.bintray.com/bugyun/maven" }//我的仓库,如果 jcenter 能找到，就不要添加
    }
}
```
然后在子项目中的 build.gradle 文件中添加
```java
dependencies {
    implementation 'vip.ruoyun.helper:screen-helper:1.0.0'
}
```

使用，在每个Activity 重写getResources()方法。
```java
public class MainActivity extends AppCompatActivity {
    @Override
    public Resources getResources() {
        return ScreenHelper.applyAdapt(super.getResources(), 450f, ScreenHelper.ScreenMode.WIDTH_DP);
    }
}
```

如果是悬浮窗适配，因为 inflate 用到的 context 是 application 级别的，所以需要在自定义的 Application 中重写 getResource。

```java
public class App extends Application {
    @Override
    public Resources getResources() {
        return ScreenHelper.applyAdapt(super.getResources(), 450f, ScreenHelper.ScreenMode.WIDTH_DP);
    }
}
```

#### 类型
- ScreenHelper.ScreenMode.WIDTH_DP 以 dp 来适配，在 xml 中使用 dp 单位
- ScreenHelper.ScreenMode.WIDTH_PT 以 pt 来适配，在 xml 中使用 pt 单位
- ScreenHelper.ScreenMode.HEIGHT_PT 以 pt 来适配，在 xml 中使用 pt 单位



