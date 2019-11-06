---
layout: post
title:  "[Android] Browse the SharedPreferences data"
date:   2018-09-20 09:00:00
tags: android sharedPreference browse security secure
categories: devstory
---
Android 개발시에, 외부에 노출되어도 되는 값들은 SharedPreference에 저장을 하게 됩니다. 그렇다면, 이 SharedPreference는 어떻게 관리되며 어디에 저장이 될까요?
아래와 같이 Preference에 쓰고/읽는 데이터들은 모두 특정 ***파일*** 에 기록되게 됩니다.

이 글에서는 이렇게 저장되는 "데이터"를 실제로 browsing하는 법을 소개합니다. (Android Studio 이용)


## SharedPreferences 에 데이터 읽고 쓰기
우선 SharedPreferences 파일을 살펴보기 전에 데이터를 저장하고 로드하여 해당 파일 생성을 하는 법을 알아봅시다. 다음 예시와 같이 ***context*** 객체를 이용하여 ***SharedPreferences*** 객체를 생성할 수 있고, 해당 객체를 이용하여 데이터를 읽고 쓸 수 있습니다.

```kotlin
val prefName = "com.toast.gamebase" // 파일명이 됩니다.
2
// SharedPreferences object 생성
val pref: SharedPreferences = context.getSharedPreferences(prefName, Context.Mode_PRIVATE)

// 데이터 쓰기를 위한 SharedPreference Editor object 생성
val key = "MyName"
val value = "PankiPark"
val editor: SharedPreferences.Editor = pref.edit()
editor.let {
    it.putString(key, value)
    it.apply()
}

// SharedPreferences object를 이용한 데이터 읽기
val defaultValue = "404 Not found"
val name = pref.getString(key, defaultValue) // PankiPark
```


예를 들어 위와 같이 데이터를 저장하였다면, 해당 preference file name은 *prefName* 변수값을 따라, `com.toast.gamebase` 이 됩니다.


## Browse the SharedPreferences file
안드로이드 스튜디오에서 `Cmd+Shift+A` 단축키를 누릅니다. 그 후, 다음과 같은 창이 뜨면, Device File Explorer를 입력하여 해당 View를 실행합니다. ( ***View > Tool Windows > Device File Explorer*** 로도 실행가능합니다.)
![1.png](/static/assets/img/posts/sharedPreference/1.png)

Device File Explorer를 실행하면 다음과 같이 adb에 연동되어있는 기기의 내용을 볼 수 있습니다.
![2.png](/static/assets/img/posts/sharedPreference/2.png)

아래의 경로로 들어가서 직접 SharedPreferences 파일을 확인해봅시다.
```/data/data/{package_name}/shared_prefs/{file_name}.xml```


위의 예시에 해당하는 파일을 찾으면, 다음과 같은 정보들을 확인 할 수 있습니다.
![3.png](/static/assets/img/posts/sharedPreference/3.png)
![4.png](/static/assets/img/posts/sharedPreference/4.png)


## Refererences
- https://developer.android.com/studio/debug/device-file-explorer?hl=ko