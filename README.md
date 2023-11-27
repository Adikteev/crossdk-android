> :warning: Due to the end of the Cross Promotion Platform, this repository is now deprecated.

# CrossDK 2.3.0

![Android](https://img.shields.io/badge/Android-android-white)
[![Kotlin](https://img.shields.io/badge/Kotlin-compatible-brightgreen)](#kotlin-support)
[![Java](https://img.shields.io/badge/Java-compatible-brightgreen)](#java-support)

## Table of contents :

* [Overview](#overview)
* [Configuration](#configuration)
* [Usage](#usage)

## Overview:

CrossDK is a solution belonging to Adikteev. The goal is to allow its users to cross-promote their
application catalog through the `CrossDKView` classes.

## Requirements:

**Android** version **>= Android 5.0**
**Android API** version **>= API 21**

The minimum android API version supported by the SDK is 21, the sdk provides support in order to
handle cases where the api is lower than 21
(see [CrossDK Listener](#crossdk-listener))

## Installation:

### Maven with Gradle:

- Inside dependencies of the build.gradle of app module, add the following dependency:

```groovy
dependencies {
  implementation 'com.adikteev:crossdk-android:2.3.0'
}
```

Gradle will directly add the package to your dependencies.

### GitHub Package Registry with Gradle:

1. Step 1 : Generate a Personal Access Token for GitHub

- Inside you GitHub account go to: Settings -> Developer Settings -> Personal Access Tokens ->
  Generate new token
- Make sure you select the following scopes (“ write:packages”, “ read:packages”) and Generate a
  token
- After Generating make sure to copy your new personal access token. You cannot see it again! The
  only option is to generate a new key

2. Step 2 : Update build.gradle inside the application module

- Add the following code to build.gradle inside the app module that will be using the SDK published
  on GitHub Package Registry

```groovy
repositories {
    maven {
        name = "GitHubPackages"
        url = uri("https://maven.pkg.github.com/Adikteev/crossdk-android")
        credentials {
            /*
            *GITHUB_USERID: your github user Id
            *PERSONAL_ACCESS_TOKEN: The generated access token
             */
            username = "GITHUB_USERID"
            password = "PERSONAL_ACCESS_TOKEN"
        }
    }
}
```

- inside dependencies of the build.gradle of app module, use the following code:

```groovy
dependencies {
  implementation 'com.adikteev:crossdk-android:2.3.0'
}
```

### Manually:

- You can also download manually the CrossDK's release package directly from
  our [Github package registry page] (https://github.com/orgs/Adikteev/packages)
- Place the release package inside a lib folder in your android studio project
- Add these lines into your app build.gradle file:

```groovy
implementation fileTree(dir: 'libs', include: ['*.jar'])
implementation files('libs/crossdk-android-release.aar')
```

- And that's it!

> Android studio gradle plugin will automatically add the CrossDK to your dependencies.

## Configuration:

In any file you'd like to use CrossDK in, don't forget to import the framework
with `import com.adikteev.crossdk.*`.

### JAVA and Kotlin support:

In order to cover a majority of projects, the CrossDK library is developed
in [Kotlin](#java-and-kotlin-support)  :

- see Kotlin support [here](#kotlin-support)
- see Java support [here](#java-support)

### Configuration:

In order to display a cross promotion properly, CrossDK requires a few information's. Since CrossDK
won't work without these, you should set them up as soon as possible. In the following example, we
use the setup function inside `Application`'s class launch but it's up to you to set it up wherever
you like, make sure you init the sdk before using `CrossDKViews`.

### kotlin support:

```kotlin
import com.adikteev.crossdk.CrossDKConfig
import com.adikteev.crossdk.network.OnInitCrossDKListener

class DemoApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        CrossDKConfig.Builder()
            .apiKey(apiKey = "<YOUR API KEY>")
            .appId(appId = "<YOUR APP ID>")
            .userId(userId = "<USER'S ID (optional)")
            .deviceId(deviceId = "<DEVICE ID (optional)")
            .setup(context = this, object : OnInitCrossDKListener {
                override fun onInitSuccess() {
                    // handle SDK init success
                }

                override fun onInitFailure(exception: Exception?) {
                    // handle SDK init failure
                }
            })
    }
}
```

### Java support:

```java
import com.adikteev.crossdk.CrossDKConfig;
import com.adikteev.crossdk.network.OnInitCrossDKListener;

public class DemoApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        new CrossDKConfig.Builder()
                .apiKey("<YOUR API KEY>")
                .appId("<YOUR APP ID>")
                .userId("<USER'S ID (optional)")
                .deviceId("<DEVICE ID (optional)")
                .setup(this, new OnInitCrossDKListener() {
                    @Override
                    public void onInitSuccess() {
                        // handle sdk init success
                    }

                    @Override
                    public void onInitFailure(@Nullable Exception exception) {
                        // handle sdk init failure
                    }
                });
    }
}
```

## Usage:

All you need to do in order to display an promotion is to add the proper view into your view
hierarchy, this can be done using the xml file of your fragment or activity, or programmatically by
adding the view to the view hierarchy The Cross promotion SDK offers three types of view

- `CrossDKView` : Banner Cross promotion format
- `CrossDKMidSizeView` : Mid size Cross promotion format
- `CrossDKInterstitialView` : Interstitial Cross promotion format with or without rewarded

### available methods:

- ```load(crossDKLoadCallback: CrossDKLoadCallback?)``` : calling this method will start pre-loading
  ad content
- ```show()``` : calling this method will show ad when added to screen
- ```setPosition(position: CrossDKPosition)``` : available on banner and mid size, set the position
  of the recommendation on screen, the available value of ```CrossDKPosition``` are BOTTOM and
  BOTTOM_RAISED
- ```setCloseButtonVisibility(visibility: Int)``` : set the visibility of the close button
- ```setCrossDKContentCallback(crossDKContentCallback: CrossDKContentCallback)``` set the callback
  for events on the corresponding view
- ```setRewarded(isRewarded: Boolean, rewardedCallback: CrossDKRewardedCallback?)``` : available on
  interstitial , calling this method will set the interstitial as rewarded

### using layout xml file:

```xml

<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" tools:context=".MainActivity">

    <com.adikteev.crossdk.views.CrossDKView android:id="@+id/crossdk_view"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_alignParentBottom="true" app:show_close_button="true" />
</RelativeLayout>
```

After adding the view to the xml you have to show the view in the fragment
or activity:

```Kotlin
import android.view.View
import android.widget.RelativeLayout
import com.adikteev.crossdk.views.CrossDKView

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val view = findViewById<CrossDKView>(R.id.crossdk_view)
        // Showing recommendation
        view.show()
    }
}
```

### using kotlin:

```Kotlin
import android.view.View
import android.widget.RelativeLayout
import com.adikteev.crossdk.views.CrossDKView

class MainActivity : AppCompatActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val container =
      findViewById<RelativeLayout>(R.id.container_id) // container_id: the id of your parent layout
    val view = CrossDKView(this)
    val params =
      RelativeLayout.LayoutParams(
        RelativeLayout.LayoutParams.MATCH_PARENT,
        RelativeLayout.LayoutParams.WRAP_CONTENT
      )
    view.id = View.generateViewId()
    view.setCloseButtonVisibility(View.VISIBLE)
    params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM, view.id)
    container.addView(view, params)
    // Showing recommendation
    view.show()
  }
}
```

### using Java:

```Java
import android.view.View;
import android.widget.RelativeLayout;

public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    RelativeLayout container = findViewById(R.id.container_view); // container_id: the id of your parent layout
    CrossDKView view = new CrossDKView(this);
    RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
            RelativeLayout.LayoutParams.MATCH_PARENT,
            RelativeLayout.LayoutParams.WRAP_CONTENT);
    view.setId(View.generateViewId());
    view.setCloseButtonVisibility(View.VISIBLE);
    params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM, view.getId());
    container.addView(view);
    // Showing recommendation
    view.show();
  }
}
```

## Preload recommendations:

You can preload recommendation before displaying it on screen, this is particularly useful when
having large assets(video) which takes time to display.
Preload should be launched before showing ads on screen in order to leave enough time to
video to be prepared.
Use the interface `CrossDKLoadCallback` to get notified when load finishes.

A recommendation is available '30min' after its preload, when this delay passed the view becomes
expired and can no longer be showed on screen.

- Kotlin

```Kotlin
import com.adikteev.crossdk.views.CrossDKInterstitialView
import com.adikteev.crossdk.views.listener.CrossDKLoadCallback

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val interstitial = CrossDKInterstitialView(this)
        // Add view to hierarchy

        //Preload recommendation
        interstitial.load(object : CrossDKLoadCallback {
            override fun onRecommendationLoaded() {
                // you can show recommendation
                interstitial.show()
            }

            override fun onRecommendationLoadFailure() {
                // handle load failure
            }

            override fun onRecommendationExpired() {
                // handle load expired
            }
        })
    }
}
```

- Java

```Java
import android.view.View;
import android.widget.RelativeLayout;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        CrossDKInterstitialView interstitial = new CrossDKInterstitialView(this);
        // Add view to view hierarchy

        //Preload recommendation
        interstitial.load(new CrossDKLoadCallback() {
            @Override
            public void onRecommendationLoaded() {
                // you can show recommendation
            }

            @Override
            public void onRecommendationLoadFailure() {
                // handle load failure
            }

            @Override
            public void onRecommendationExpired() {
                // handle load expired
            }
        });
    }
}
```

Make sure to leave enough time for the recommendation to fully load, the best way of doing this is
to load the recommendation in advance before showing in on screen, To show the view use
the `show()` method on the mid size and interstitial object

```Kotlin
interstitial.show()
```

## Rewarded interstitial:

You can set an interstitial to be rewarded using the
method `setRewarded(isRewarded: Boolean, rewardedCallback: CrossDKRewardedCallback?)` by setting the
parameter `isRewarded` to `true`. Additionally you can get notified when user should be rewarded
using the interface `CrossDKRewardedCallback`

## CrossDK Listener:

Additionally, an interface is available if you want to monitor what is happening with
the `CrossDKView`.

### using Kotlin:

```kotlin
import com.adikteev.crossdk.views.CrossDKView
import com.adikteev.crossdk.views.listener.CrossDKContentCallback

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val view = CrossDKView(this)
        view.setCrossDKContentCallback(object : CrossDKContentCallback {
            override fun onRecommendationDisplayed() {
                // handle recommendation display
            }

            override fun onRecommendationClicked() {
                // handle recommendation clicked
            }

            override fun onRecommendationClosed() {
                // handle recommendation closed
            }
            override fun onUnsupportedApiVersion() {
                // handle lower api
            }

            override fun onConfigurationError() {
                // handle configuration error
            }

            override fun onNoRecommendation() {
                // handle no recommendation case
            }

            override fun onShowContentError() {
                // handle show content error
            }
        })
    }
}
```

### using Java:

```Java
import com.adikteev.crossdk.views.CrossDKView;
import com.adikteev.crossdk.views.listener.CrossDKContentCallback;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        CrossDKView view = new CrossDKView(this);
        view.setCrossDKContentCallback(new CrossDKContentCallback() {
            @Override
            public void onRecommendationDisplayed() {
                // handle recommendation display
            }

            @Override
            public void onRecommendationClicked() {
                // handle recommendation clicked
            }

            @Override
            public void onRecommendationClosed() {
                // handle recommendation closed
            }

            @Override
            public void onUnsupportedApiVersion() {
                // handle lower api
            }

            @Override
            public void onConfigurationError() {
                // handle configuration error
            }

            @Override
            public void onNoRecommendation() {
                // handle no recommendation case
            }

            @Override
            public void onShowContentError() {
                // handle show content error
            }
        });
    }
}
```

## CrossDK lifecycle:

You can dismiss the promotion by calling the method `.dismissView(isAnimated: Boolean)`, if the
promotion is not properly dismissed you can call the method `.destroy()` available in all the
CrossDKView's in your `.onDestroy()` or `.onDetach()` lifecycle methods in order to to clean the
view

## CrossDK Debug mode:

You can enable debug mode on SDK in order to have logs in console, this can be done by setting the
logLevel in `CrossDKConfig` class:

```kotlin
import com.adikteev.crossdk.CrossDKConfig

CrossDKConfig.Setting.logLevel = CrossDKConfig.LOG.VERBOSE
```

- `LOG.VERBOSE`: for verbose logging
- `LOG.ERROR`: for error logging
- `LOG.NONE`: for disabling logger

> We recommend setting up this config before initializing the SDK.

## Soft keyboard management:

The CrossDK content hides soft keyboard when displayed for the first time.
Please note that this behavior may not work on all keyboard configurations. It's always preferable
to handle the dismiss and display of the keyboard in application level.

## Hardware acceleration:

CrossDK needs hardware acceleration enabled in order to properly show animated content, so make sure
you have this activated in your AndroidManifest.xml:

```Xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
  <activity android:hardwareAccelerated="true" />
</manifest>
```

## Proguard:

If you are using R8 the shrinking and obfuscation rules are included automatically. In other cases
you might need to include these rules:

```Gradle
-keep class com.adikteev.crossdk.** {*;}
-keepclasseswithmembers class com.adikteev.crossdk.** {*;}
```

That’s all you need to know !
