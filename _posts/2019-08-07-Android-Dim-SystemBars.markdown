---
layout: post
title:  "[Android] Dim The System Bars"
date:   2019-08-07 12:00:00
tags: android ui dim statusbar navigationbar systembar systemui visibility
categories: devstory
---
# Dim The System Bars

**StatusBar** 및 **NavigationBar** 를 dimmed 처리 할 수 있는 방안에 대해서 다룹니다.
Android 4.0 (API 14) 이상에서 동작하며, 이전 버전에서는 dimmed 할 수 있는 빌트인된 API를 제공하지는 않습니다.

아래 API를 사용해서 System Bar를 dimmed 되게 처리할 수 있습니다.
사용자가 해당 bar를 터치하면, dimmed 처리가 사라지고 완전하게 보여지게 됩니다.

> 다시 system bars의 dimmed 처리를 원한다면 아래 메소드를 재호출해주어야합니다.

### Dim the system bars
**SYSTEM_UI_FLAG_LOW_PROFILE** 플래그를 사용하면, SystemBar의 Dimmed 처리를 할 수 있습니다.

```kotlin
// DecorView 뿐만 아니라, 보여지는 다른 View들에도 적용가능합니다.
activity?.window?.decorView?.apply {
    systemUiVisibility = View.SYSTEM_UI_FLAG_LOW_PROFILE
}
```

### Undim the system bars programmatically
만약 코드상으로 undimmed 처리를 원한다면 다음과 같이 호출해줍니다.

```kotlin
// DecorView 뿐만 아니라, 보여지는 다른 View들에도 적용가능합니다.
activity?.window?.decorView?.apply {
    systemUiVisibility = 0
}
```
