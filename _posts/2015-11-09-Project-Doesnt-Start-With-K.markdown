---
layout: post
title:  "Project doesnt start with K, laying the foundation."
date:   2015-11-09 20:50:47
categories: kotlin
featured: true
comments: true

imagefeature: images/intro.jpg
---
Welcome back!
The time has come to define ( perhaps redefine ) the scope of our intended project. The original intent was to leverage the Rotten Tomatoes API to generate a reasonable starter application in Kotlin. The intended goals were a application that made network calls, parsed json, and used the data to populate some sort of adapter view. The Rotten Tomatoes API now requires registering for, and getting approval to use, an API key to access their data.

Rather than introduce that burden of responsibility for anyone that was interested in following along,  we will instead use the [SeatGeek API](http://platform.seatgeek.com/) which gives us the benefits of a well organized and documented open API for our use. 

General setup can easily be broken down into a couple of requirements

### IDE Plugins
These are readily obtained by going to Android Studio -> Preferences -> Plugins -> Browse Respoitories:

    1. Kotlin Plugin
    2. Kotlin Android Extensions
    
### Gradle modifications
App level changes

{% highlight groovy %}

apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'

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
    compile "com.android.support:design:$support_version"
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    compile "org.jetbrains.anko:anko-sdk15:$anko_version"

}

buildscript {
    ext.support_version = '23.1.0'
    ext.kotlin_version = '1.0.0-beta-1038'
    ext.anko_version = '0.7.2'
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath "org.jetbrains.kotlin:kotlin-android-extensions:$kotlin_version"

    }
}
repositories {
    mavenCentral()
}


{% endhighlight %}

Note,  the above defining of a kotlin srcDir is optional.  However it will likely be helpful to keep our files seperate for now.  Also, the buildscript that contains the dependencies for the kotlin-android-extensions, and gradle-plugin needed to be in my App level build.gradle file.  I have seen documentation and examples that put this in the project level gradle file,  but i was never able to utilize the android extensions functionality when doing this.

### Java conversion(optional)

One of the great features that eases the transition from Java to Kotlin, is the ability to have the IDE convert any of your existing Java classes into a kotlin file.  As an example when using this feature when choosing the new  Activity with a FAB template from Android Studio,  we are left with the following  kotlin compliant MainActivity class,  which serves as a valuable reference point as we start this journey.

{% highlight kotlin %}

package com.ruthlessprogramming.doesntstartwithk


import android.os.Bundle
import android.support.design.widget.Snackbar
import android.support.v7.app.AppCompatActivity
import android.view.View
import android.view.Menu
import android.view.MenuItem

import kotlinx.android.synthetic.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        setSupportActionBar(toolbar)

        fab.setOnClickListener(object : View.OnClickListener {
            override fun onClick(view: View) {
                Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG).setAction("Action", null).show()
            }
        })
    }

    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        // Inflate the menu; this adds items to the action bar if it is present.
        menuInflater.inflate(R.menu.menu_main, menu)
        return true
    }

   override  fun onOptionsItemSelected(item: MenuItem): Boolean {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        val id = item.itemId

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true
        }

        return super.onOptionsItemSelected(item)
    }
}

{% endhighlight %}

Take note of the following import: **import kotlinx.android.synthetic.activity_main.**

This import is a convenience feature provided to us by Kotlin's Android Extensions. It negates the necessity of performing a findViewById() lookup,  and provides direct access to the widgets by using their id from xml. 
This makes the following code possible without any other explicit imports, casting, or  id lookup.

{% highlight kotlin %}

    setSupportActionBar(toolbar)
    
{% endhighlight %}

At this point, MainActivity compiles and is able to be launched through an emulator.  We will use this foundation and improve upon it with a RecyclerView,  and use it to display some content from the Rotten Tomatoes API.  

    


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
