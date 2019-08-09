---
layout: post
title:  "[Android] Hide The System Bars"
date:   2019-08-08 12:00:00
tags: android ui hide statusbar navigationbar systembar systemui visibility
categories: devstory
---
# Hide the System Bar

이번 시간에는 Android System Bar를 숨김처리하는 방법에 대해서 다뤄보겠습니다.
Android System Bar는 크게 2가지가 있습니다.

디바이스 최상단에 각종 노티피케이션들이 노출되는 영역인 **Status Bar** 과, 디바이스 하단에 위치하여 앱의 이동 등을 돕는 **Navigation Bar** 가 있습니다.

아래에서 2가지 System Bar 를 숨김처리하여, 보다 몰입감 있는 어플리케이션을 만들어봅시다.

주의할 점은 역시 "몰입감"을 위해서 "사용자의 편의성"을 해치면 안된다는 것이겠지요? 즉, 아무때나 화면을 확장처리하는 것은 좋지 못한 디자인이라는 것만 명심하고, 작업해봅시다.

<br/>
<br/>

## Hide The Status Bar

Status bar는 디바이스 상단에 각종 노티피케이션들이 노출되는 영역입니다.
한가지 짚고 넘어갈 점은, **Status bar를 감출 때는 Action bar도 감춰야한다**는 것입니다.

![](/static/assets/img/posts/android-hide-system-ui/hide-systemui-status-bar-01.png)
<Visible Status bar>

![](/static/assets/img/posts/android-hide-system-ui/hide-systemui-status-bar-02.png)
<Hidden Status bar + Hidden Action Bar>

---

Status bar를 감추기 위해서는 버전을 나눠서 생각할 필요가 있습니다.
Android 4.0(API 14) 이하와 Android 4.1(API 16) 이상으로 나눠서 설명합니다.


### 1. Android 4.0 이하
Android 4.0 이하에서는 다음 2가지 방법이 있습니다. 
둘 다 **[WindowManager](https://developer.android.com/reference/android/view/WindowManager.html)** 를 이용한 방법입니다.

#### 1) Android Manifest theme 이용
```xml
<application
    ...
    android:theme="@android:style/Theme.Holo.NoActionBar.Fullscreen" >
    ...
</application>
```

activity theme을 이용하는 것은 다음과 같은 장점이 있습니다.
1. 코드로 작성하는 것보다 안정적이며, 유지보수가 용이합니다.
2. activity가 초기화되기 전에 시스템이 UI에 대한 정보를 알고 있으므로, 보다 매끄러운 UI 트랜지션을 보여줍니다.


#### 2) 코드
코드 상에서 ***WindowManager*** 플래그를 이용하여 설정할 수 있는 방법이 있습니다. 유저의 인터렉션에 따라 status bar를 감췄다가 노출시키는 등의 처리를 쉽게 할 수 있습니다.

```kotlin
class MainActivity : Activity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // If the Android version is lower than Jellybean, use this call to hide
        // the status bar.
        if (Build.VERSION.SDK_INT < 16) {
            window.setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                    WindowManager.LayoutParams.FLAG_FULLSCREEN)
        }
        setContentView(R.layout.activity_main)
    }
    ...
}
```

[FLAG_LAYOUT_IN_SCREEN](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_LAYOUT_IN_SCREEN)을 설정해주면, [FLAG_FULLSCREEN](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_FULLSCREEN)을 활성화 시켰을 때의 스크린 사이즈를 동일하게 activity layout에서 사용 할 수 있도록 해줍니다.
**[FLAG_LAYOUT_IN_SCREEN](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_LAYOUT_IN_SCREEN)** 를 사용하면, 앱 컨텐츠가 status bar가 사라지고 보여질 때, **resize 되는 것을 방지** 해줍니다.


### 2. Android 4.1 이상
Android 4.1 이상에서는 **[setSystemUiVisibility()](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility%28int%29)** API를 사용하여 status bar를 감출 수 있습니다.

**[setSystemUiVisibility()](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility%28int%29)** 는 각기 View Level에서 UI 플래그를 설정합니다. 그리고 이 설정들은 Window Level에서 취합되어 계산됩니다.
이 API는 **[WindowManager](https://developer.android.com/reference/android/view/WindowManager.html)** 를 사용하는 것보다 더 세밀하게 system bar를 컨트롤 할 수 있습니다.

아래 코드로 status bar를 감출 수 있습니다.

```kotlin
// Hide the status bar.
window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_FULLSCREEN
// Remember that you should never show the action bar if the
// status bar is hidden, so hide that too if necessary.
actionBar?.hide()
```


Android 4.1 이상에서는 status bar 뒤로 컨텐츠를 표시 할 수 있습니다. 그래서, 컨텐츠가 status bar 가 사라지고 보여질 때, **resize되지 않습니다.**

**컨텐츠의 resize 를 막기 위해서는** **[SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN](https://developer.android.com/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN)** 설정이 필요합니다.

또한 **[SYSTEM_UI_FLAG_LAYOUT_STABLE](https://developer.android.com/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_STABLE)** 플래그를  사용하여 안정적인 레이아웃을 유지할 수 있도록 하세요.

하지만, 위 방법을 사용하여, status bar 뒤에 컨텐츠가 들어가게 된다면, 특정 UI 인터렉션이 불가능 할 수 있습니다.
이를 막기 위하여, **android:fitsSystemWindows** 어트리뷰트를 **layout XML** 파일에 정의하고, **true**로 설정 할 수 있습니다. 이렇게하면, 시스템 윈도우를 위한 여분의 공간에 패딩을 적용해줍니다.

만약, 앱에서 원하는 특정 패딩값이 있을 수 있습니다. 이를 조작하려면, **[fitSystemWindows(Rect insets)](https://developer.android.com/reference/android/view/View.html#fitSystemWindows%28android.graphics.Rect%29)** 메소드를 오버라이드하여 뷰를 그릴 수 있습니다.

<br/>
<br/>

## Hide The Navigation Bar

Navigation bar는 Android 4.0(API 14)에 소개된 기능입니다. navigation bar를 감출 때는 status bar도 함께 감추도록 디자인되어야합니다.

![](/static/assets/img/posts/android-hide-system-ui/hide-systemui-navigation-bar.png)
<Navigation bar>


Navigation bar는 다음 API 호출로 쉽게 감출 수 있습니다.

```kotlin
fun hideNavigationBar() {
    window.decorView.apply {
        // Hide both the navigation bar and the status bar.
        // SYSTEM_UI_FLAG_FULLSCREEN is only available on Android 4.1 and higher, but as
        // a general rule, you should design your app to hide the status bar whenever you
        // hide the navigation bar.
        systemUiVisibility = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or View.SYSTEM_UI_FLAG_FULLSCREEN
    }
}
```

위와 같이 설정을 하면 navigation bar를 감출 수 있습니다.
다만, 유저의 터치 이벤트가 발생한다면, navigation bar(및 status bar)가 다시 노출됩니다. 만약 다시 감추기를 원한다면 위의 API를 재호출해주어야합니다.

Android 4.1 이상에서는 navigation bar 뒤로 컨텐츠를 표시 할 수 있습니다. 그래서, 컨텐츠가 navigation bar 가 사라지고 보여질 때, **resize되지 않습니다.**

**컨텐츠의 resize 를 막기 위해서는** **[SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION)** 설정이 필요합니다.

또한 **[SYSTEM_UI_FLAG_LAYOUT_STABLE](https://developer.android.com/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_STABLE)** 플래그를  사용하여 안정적인 레이아웃을 유지할 수 있도록 하세요.

하지만, 위 방법을 사용하여, status bar 뒤에 컨텐츠가 들어가게 된다면, 특정 UI 인터렉션이 불가능 할 수 있습니다.
이를 막기 위하여, **android:fitsSystemWindows** 어트리뷰트를 **layout XML** 파일에 정의하고, **true**로 설정 할 수 있습니다. 이렇게하면, 시스템 윈도우를 위한 여분의 공간에 패딩을 적용해줍니다.

만약, 앱에서 원하는 특정 패딩값이 있을 수 있습니다. 이를 조작하려면, **[fitSystemWindows(Rect insets)](https://developer.android.com/reference/android/view/View.html#fitSystemWindows%28android.graphics.Rect%29)** 메소드를 오버라이드하여 뷰를 그릴 수 있습니다.
