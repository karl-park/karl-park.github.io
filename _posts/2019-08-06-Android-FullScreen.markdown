---
layout: post
title:  "[Android] Enable Full Sceen Mode"
date:   2019-08-06 12:00:00
tags: android ui fullsreen leanback imersive imersivesticky systemui visibility navigationbar statusbar
categories: devstory
---
# Enable Full Sceen Mode

작은 화면보다는 큰 화면이 사용자에게 더욱 큰 몰입감을 제공합니다. 이것은 TV나 모니터 뿐만이 아니라, "안드로이드 앱"에도 해당되는 말입니다.

Android에는 Fullscreen 모드가 있습니다. System navigation UI 영역 및 Status bar 영역까지 앱의 화면을 확장하는 것이죠.
몰입감을 위해 우리의 앱을 Fullscreen 모드로 설정하고 싶은 마음이 스물스물 올라올 때가 있습니다.

이렇듯 우리가 만든 앱을 더욱 근사하게 만들어주는 Fullscreen 모드를 아무때나 사용하면 될까요?

> Fullscreen 모드는 system navigation에 대한 접근도를 떨어뜨립니다.
> 즉, 사용자에게 `그것을 감내할만한 근사한 경험을 주는 경우에 한해서` Fullscreen 모드를 사용해야합니다.
>
> 예) 게임, 이미지/비디오 뷰어, 책 뷰어 등

## Fullscreen options

Android에서는 Fullscreen 을 위해서는 다음 3가지 옵션을 제공합니다.
모든 옵션이 System bar들을 숨김 처리하며, 앱의 Activity가 모든 터치 이벤트를 받을 수 있도록 합니다. 이 옵션들의 차이점은 사용자가 어떻게 system bar들을 다시 불러오는지에 대한 것입니다.

### 1. Lean Back

***Lean Back*** 이라는 단어는 "소파등에 기대서 즐기는 어떤것" 이라는 뉘앙스가 있습니다. 즉 영화 감상 등을 한다고 생각하면 됩니다.
우리가 Android 앱에서 영화 등의 비디오 컨텐츠를 볼 때 어떨지 떠올려보세요. 화면을 **터치하는 것만으로도** system bars들이 나타나는 것을 볼 수 있습니다.

이러한 특징을 가지는 것이 **Lean Back** 방법입니다.


#### Lean Back 모드 설정

Lean Back을 설정하기 위해서는 다음과 같은 API를 호출해줍니다.

```kotlin
fun applyFullscreenWithLeanBackMode() {
    window.decorView.systemUiVisibility = (
        View.SYSTEM_UI_FLAG_FULLSCREEN
        or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION)
}
```

### 2. Immersive

Immersive 모드는 사용자가 앱 사용시, 온전한 몰입을 필요로 할 때 사용됩니다.
게임이나, 갤러리에서 사진을 볼 때, 혹은 책, 슬라이드 등과 같은 컨텐츠를 보는 등, 앱이 사용자와의 인터렉션이 활발하게 이뤄져야 할 때 사용합니다.
화면에서 system bars를 불러오려면 해당 bars들이 있는 곳을 사용자가 **swipe** 해주어야합니다.

#### Immersive 모드 설정

Immersive 모드를 설정하기 위해서는 다음과 같은 API를 호출해줍니다.

```kotlin
fun applyFullscreenWithImmersiveMode() {
    window.decorView.systemUiVisibility = (
        View.SYSTEM_UI_FLAG_IMMERSIVE
        or View.SYSTEM_UI_FLAG_FULLSCREEN
        or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION)
}
```

### 3. Immersive Sticky

Immersive Sticky 모드는 Immersive 모드의 한계에 대응하기 위해 제공됩니다.
Immersive 모드에서는 system bars를 불러오기 위해 system bars 영역에 swipe를 하게 되는데요 ~ 앱에서는 해당 이벤트를 받을 수 없습니다.

만약 해당 영역의 swipe 이벤트를 받으려면 **Immersive Sticky** 모드를 사용해야합니다.

Immersive Sticky 모드에서는 사용자가 해당 영역을 swipe할 시, 반투명 처리된 system bars들이 노출되게 되며, swipe 이벤트가 "앱으로 전달"되게 됩니다.

#### Immersive Sticky 모드 설정
Immersive Sticky 모드는 다음과 같이 설정합니다.

```kotlin
fun applyFullscreenWithImmersiveStickyMode() {
    window.decorView.systemUiVisibility = (
        View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        or View.SYSTEM_UI_FLAG_FULLSCREEN
        or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION)
}
```

#### UI Visibility Changes Event
주의할점이 있습니다.
*Immersive Sticky 모드에서는 UI Visibility Changes Event 콜백을 받을 수 없습니다.*


## Fullscreen and Soft keyboard
layout xml의 `android:windowSoftInputMode="adjustResize"` 구문은 `WindowManager.LayoutParams.FLAG_FULLSCREEN` 설정시, 동작하지 않습니다.
즉, "Fullscreen" 환경에서는 soft keyboard가 activity window를 resize 하지 못합니다.
그러면 어떻게 해야할까요?

> 사실 방법이 없습니다. "Fullscreen" 모드를 해제합니다.

아래 코드처럼 해제를 하거나, xml에서 해제합니다.

```kotlin
window.addFlags(WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN)
window().clearFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN)
```


## UI Visibility Changes Event
사용자의 터치 등으로 인해 System Bar들이 나타나고 사라지는 것에 대한 이벤트 콜백을 다음과 같이 받을 수 있습니다.

```kotlin
window.decorView.setOnSystemUiVisibilityChangeListener { visibility ->
    if (visibility and View.SYSTEM_UI_FLAG_FULLSCREEN == 0) {
        // System Bar가 보여지고 있습니다.
    } else {
        // System Bar가 안보이고 있습니다.
    }
}
```

# References
- [Control the system UI visibility](https://developer.android.com/training/system-ui)
- [system-ui](https://developer.android.com/training/system-ui/immersive)
- [Android ImmersiveMode Sample](https://github.com/googlesamples/android-ImmersiveMode/)
- [Android BasicImmersiveMode Sample](https://github.com/googlesamples/android-BasicImmersiveMode/)
- [Android AdvancedImmersiveMode Sample](https://github.com/googlesamples/android-AdvancedImmersiveMode/)
- [Problem Solved 3: Android Full Screen View + Translucent + ScrollView + AdjustResize + Keyboard](https://medium.com/@sandeeptengale/problem-solved-3-android-full-screen-view-translucent-scrollview-adjustresize-keyboard-b0547c7ced32)
    - API 21 이상에서 동작하고, Nested Scroll View를 이용한 구현(android:fitsSystemWindows)
- Google Issue Tracker
    - [WebView adjustResize windowSoftInputMode breaks when activity is fullscreen](https://issuetracker.google.com/issues/36911528)
- Stackoverflow
    - [Android How to adjust layout in Full Screen Mode when softkeyboard is visible
](https://stackoverflow.com/questions/7417123/android-how-to-adjust-layout-in-full-screen-mode-when-softkeyboard-is-visible/19494006#19494006)

