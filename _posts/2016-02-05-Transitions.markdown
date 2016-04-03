---
layout: post
title:  "Life transitions and a project refresh."
date:   2016-02-20 19:50:47
categories: life
featured: true
comments: true
---

Much has changed since the last post. Over the fall and winter i was able to wrap up some of my main projects at my previous employer, and was beginning to get the itch to work on something new. Enter [RapidSOS](http://rapidsos.com)!
After meeting with a range of their personnel, they presented the combination of a fresh technical challenge, with the added benefit of a product that will genuinely make a difference in peoples livess.

In addition to changing employers,  much has changed in the world of Kotlin since i last posted.  It has now hit a stable 1.0 release,  and with every iteration it becomes increasingly clear that the language designers are treating Android as a first class citizen within their ecosystem. Kotlins commitment to support Java 6 bytecode as long as Android needs it is encouraging. Given the relatively seamless interoperability with pure java code, it's hard to argue against the value proposition Kotlin provides.

Given this,  it's time to dust off the Kotlin exploration project and get back to work. 

### Project Refresh
There is a Kotlin plugin bundled into the community edition of IntelliJ  now, and this plugin is also available and suitable for development within Android Studio.  One notable change is that the previous Android Extensions plugin is now lumped into / included with the main Kotlin plugin. You no longer need to install this seperately, just apply it alongside the kotlin plugin in your build.gradle  and you can continue to leverage all of the convenience it offered.  We will still be including Anko,  even though its full usefulness will likely not be tapped until our project grows in complexity.  

The new Kotlin plugin will allow you to configure Kotlin within your project,  as a gradle based project,  and covering either a single module or all modules.  You also have a handy selector through which you can choose previous versions of the plugin to implement,  if you wanted to do that.  The plugin is first found by going to Settings -> Plugins ->  External Repositories,  and then searching for "Kotlin Plugin"  similar to how you would install any other plugin.

In addition, the Fuel networking library we had experimented with previously has undergone changes of its own,  in accordance with  the updates to the Kotlin. The semantics around result handling have changed slightly, but in general the process is still familiar, and we will delve into it in earnest in the following post ( which is already mostly written ).



In general will be able to get by with just a couple of changes

    1. Remove android-extensions dependency
    2. Increment build tools. 
    3. Increment kotlin version, anko version, and android support libraries
    4. Increment 3rd party libraries  (currently only using fuel)

Wrapping our refresh in preperation of revisiting the projects leaves us with the following changes to our gradle file (note due to problems with instance run,  i have left the main gradle plugin version where it was previously).



{% highlight kotlin %}

buildscript {
    ext.kotlin_version = '1.0.1-2'
    ext.support_version = '23.2.1'
    ext.anko_version = '0.8.3'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.1"
    defaultConfig {
        applicationId "com.ruthlessprogramming.doesntstartwithk"
        minSdkVersion 16
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
    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }
}



dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile "com.android.support:appcompat-v7:$support_version"
    compile "com.android.support:support-v4:$support_version"
    compile "com.android.support:design:$support_version"
    compile "org.jetbrains.anko:anko-sdk15:$anko_version"
    compile "org.jetbrains.anko:anko-support-v4:$anko_version "
    compile "org.jetbrains.anko:anko-appcompat-v7:$anko_version"
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    //networking library
    compile 'com.github.kittinunf.fuel:fuel-android:1.1.3'

}

{% endhighlight %}

    


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
