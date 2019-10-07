---
layout: post
title:  "[Android] Translucent Activity Crash on Android Oreo (Android 8.0.0)"
date:   2019-09-25 07:00:00
tags: android oreo activity crash issue translucent opaque styles orientation
categories: devstory
---

# Only fullscreen opaque activities can request orientation

## Problem

Target API 27 이상인 코드에 대하여, **`Android API Level 26`** (Android 8.0.0, Oreo) 기기에서 **`만`** 오동작하는 경우가 있다.

다음과 같은 에러 로그를 뿜으며 앱이 죽는다.

```log
    --------- beginning of system
2019-09-17 11:03:42.725 2577-5200/? I/ActivityManager: START u0 {flg=0x34000000 cmp=com.eungpang.rivu/com.eungpang.rivu.ui.TranslucentActivity} from uid 10083

    --------- beginning of crash
2019-09-17 11:03:42.746 7135-7135/com.eungpang.rivu E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.eungpang.rivu, PID: 7135
    java.lang.RuntimeException: Unable to start activity ComponentInfo{com.eungpang.rivu/com.eungpang.rivu.ui.TranslucentActivity}: java.lang.IllegalStateException: Only fullscreen opaque activities can request orientation
        at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2817)
        at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2892)
        at android.app.ActivityThread.-wrap11(Unknown Source:0)
        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1593)
        at android.os.Handler.dispatchMessage(Handler.java:105)
        at android.os.Looper.loop(Looper.java:164)
        at android.app.ActivityThread.main(ActivityThread.java:6541)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.Zygote$MethodAndArgsCaller.run(Zygote.java:240)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:767)
     Caused by: java.lang.IllegalStateException: Only fullscreen opaque activities can request orientation
        at android.app.Activity.onCreate(Activity.java:986)
        at com.eungpang.rivu.ui.TranslucentActivity.onCreate(TranslucentActivity.java:57)
        at android.app.Activity.performCreate(Activity.java:6975)
        at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1213)
        at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2770)
        at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2892) 
        at android.app.ActivityThread.-wrap11(Unknown Source:0) 
        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1593) 
        at android.os.Handler.dispatchMessage(Handler.java:105) 
        at android.os.Looper.loop(Looper.java:164) 
        at android.app.ActivityThread.main(ActivityThread.java:6541) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.Zygote$MethodAndArgsCaller.run(Zygote.java:240) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:767) 
```

## Reason

먼저 Android API 26 에 커밋되어있는 **[Activity.onCreate() 소스코드](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-8.0.0_r38/core/java/android/app/Activity.java)** 를 보자

### Activity.java code

아래 onCreate() 메소드의 두번째 if 문을 보면, **fixedOrientation** 일 경우에 한하여, **isTranslucentOrFloating** 구문으로 **IllegalStateException("Only fullscreen opaque activities can request orientation")** 을 throw 하는 것을 확인 할 수 있다.

- URL : https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-8.0.0_r38/core/java/android/app/Activity.java

```java
// Activity.java - Android API Level 8.0.0
public class Activity extends ContextThemeWrapper
        implements LayoutInflater.Factory2,
        Window.Callback, KeyEvent.Callback,
        OnCreateContextMenuListener, ComponentCallbacks2,
        Window.OnWindowDismissedCallback, WindowControllerCallback,
        AutofillManager.AutofillClient {
    ...
    @MainThread
    @CallSuper
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        ...

        // Note: In this code block, Android throws the exception. 
        if (getApplicationInfo().targetSdkVersion > O && mActivityInfo.isFixedOrientation()) {
            final TypedArray ta = obtainStyledAttributes(com.android.internal.R.styleable.Window);
            final boolean isTranslucentOrFloating = ActivityInfo.isTranslucentOrFloating(ta);
            ta.recycle();
            if (isTranslucentOrFloating) {
                throw new IllegalStateException(
                        "Only fullscreen opaque activities can request orientation");
            }
        }

        ...
    }
    ...
}
```

### ActivityInfo.java code

isFixedOrientation() 및 isTranslucentOrFloating() 은 다음과 같이 ActivityInfo 클래스에서 처리하고 있다. 즉, **Activity의 Orientation이 고정되어있고** Translucent style이 true로 정의되어있으면, API 26 기기에서 "항상 발생"한다고 말 할 수 있다.

- URL : https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-8.0.0_r38/core/java/android/content/pm/ActivityInfo.java

```java
// ActivityInfo.java - Android API Level 8.0.0
public class ActivityInfo extends ComponentInfo implements Parcelable {
    ...
    public static boolean isTranslucentOrFloating(TypedArray attributes) {
        final boolean isTranslucent =
                attributes.getBoolean(com.android.internal.R.styleable.Window_windowIsTranslucent,
                        false);
        final boolean isSwipeToDismiss = !attributes.hasValue(
                com.android.internal.R.styleable.Window_windowIsTranslucent)
                && attributes.getBoolean(
                        com.android.internal.R.styleable.Window_windowSwipeToDismiss, false);
        final boolean isFloating =
                attributes.getBoolean(com.android.internal.R.styleable.Window_windowIsFloating,
                        false);
        return isFloating || isTranslucent || isSwipeToDismiss;
    }
    ...

    static public boolean isFixedOrientation(int orientation) {
        return isFixedOrientationLandscape(orientation) || isFixedOrientationPortrait(orientation);
    }

    public static boolean isFixedOrientationLandscape(@ScreenOrientation int orientation) {
        return orientation == SCREEN_ORIENTATION_LANDSCAPE
                || orientation == SCREEN_ORIENTATION_SENSOR_LANDSCAPE
                || orientation == SCREEN_ORIENTATION_REVERSE_LANDSCAPE
                || orientation == SCREEN_ORIENTATION_USER_LANDSCAPE;
    }

    public static boolean isFixedOrientationPortrait(@ScreenOrientation int orientation) {
        return orientation == SCREEN_ORIENTATION_PORTRAIT
                || orientation == SCREEN_ORIENTATION_SENSOR_PORTRAIT
                || orientation == SCREEN_ORIENTATION_REVERSE_PORTRAIT
                || orientation == SCREEN_ORIENTATION_USER_PORTRAIT;
    }
}
```

## Solve

isTranslucentOrFloating의 경우 R.styleable 을 참조하여 확인하므로, 런타임에 해당 값을 변경해주는 것은 의미가 없다.

그렇다면 다음과 같은 방법이 있겠다.

1. API 26 기기에 한하여, orientation 고정을 해제한다.
    - 아래처럼, **setRequestedOrientation** 메소드를 override 하여, API 26 기기에서는 orientation을 고정하지 않도록 해준다.
    - **`API 26에서는 orientation 고정이 없으므로, 회전시 UI 가 정상적으로 노출되는지를 꼭 확인해야한다.`**
    ```java
    @Override
    public void setRequestedOrientation(int requestedOrientation) {
        if (Build.VERSION.SDK_INT != Build.VERSION_CODES.O) {
            super.setRequestedOrientation(requestedOrientation);
        }
    }
    ```
2. AndroidManifest.xml 에서 정의된 activity orientation을 해제한다.
    - orientation 고정을 위해 AndroidManifest.xml의 activity attributes로 screenOrientation 을 사용하였다면 **unspecified** 로 해제해준다.
    - **`이 방법은 모든 API Level에 대하여 화면 회전을 가능케하므로, UI 확인 등 테스트를 꼭 거쳐야한다.`**
    ```xml
    <!-- AS-IS -->
    <activity android:name=".MainActivity"
        android:screenOrientation="portrait"
        android:theme="@style/AppTheme.Transparent"/>
        
    <!-- TO-BE -->
    <activity android:name=".MainActivity"
        android:screenOrientation="unspecified"
        android:theme="@style/AppTheme.Transparent"/>
    ```
3. styles.xml 을 Android API Level 별로 분기하도록 조치한다.
    - 결국은 Android O(8.0.0, API 26)에서만 발생하는 문제이다. (이후 바로 수정됨)
    - 그렇기에 API 26에서만 다르게 동작하는 styles.xml을 정의함으로써, 문제를 우회 할 수 있다.
    - values-v27/styles.xml 을 생성하여, API 27 이상부터는 다시 투명 처리가 되도록 선언해야한다.
    - `API 26에서만, "투명" 처리가 되지 않으므로, UI 확인이 필요하다.`
    - ![variation of styles](/static/assets/img/posts/translucentActivity/variationOfStyles.png)
    ```xml
    <!-- values-v26/styles.xml -->
    <style name="MyTheme" parent="@android:style/Theme.DeviceDefault">
       ...
         <!-- set it to false -->
        <item name="android:windowIsTranslucent">false</item>
    </style>
    ```
