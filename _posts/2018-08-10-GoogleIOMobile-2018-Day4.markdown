---
layout: post
title:  "[Android] Google for Mobile I/O Recap 2018 Day4"
date:   2018-08-10 09:00:00
tags: java Android AndroidP Android9 GoogleI/O 2018
categories: devstory
---

# Google Play Instant

6/25일부터 26일, 양일간 열린 Google for Mobile I/O Recep 2018 Seoul(이하 Google I/O) 참가후기에 대한 글입니다.

이번 글에서는 [지난 시간의 글 - 개발자 세션 및 AI 세션](h/devstory/2018/08/10/GoogleIOMobile-2018-Day3/)에 이어서, Google Play Instant 앱에 대한 코드랩 내용이 다뤄집니다.

해당 세션은 6월 26일 코드랩 시간에 진행되었으며, 기존에 가지고 있던 앱을 Instant App으로 바꿔보는 실습시간으로 진행되었습니다.

### Posts
- [Google for Mobile I/O Recap_001](/devstory/2018/08/10/GoogleIOMobile-2018-Day1/) <br/>
- [Google for Mobile I/O Recap_002](/devstory/2018/08/10/GoogleIOMobile-2018-Day2/) <br/>
- [Google for Mobile I/O Recap_003](/devstory/2018/08/10/GoogleIOMobile-2018-Day3/) <br/>
- [Google for Mobile I/O Recap_004](/devstory/2018/08/10/GoogleIOMobile-2018-Day4/) <br/>



## Exhibit A - Analyze the APK deeply

- FAQ : https://developer.android.com/topic/google-play-instant/faqs


### API 제약
Android 4.1(API 레벨 16) 이상을 실행하고 Google Play 서비스가 설치된 기기와 호환


### targetSdkVersion
Your instant app's manifest must set `targetSdkVersion to 26 or higher`.


### Networking
All the network traffic from inside instant apps must use `HTTPS`. Instant apps doesn't support HTTP.

### Permission 제약
INSTANCE APP에서는 Permission에 대한 제약이 있다.
아래의 퍼미션만 사용가능하다.

```
 android.permission.BILLING
 android.permission.ACCESS_COARSE_LOCATION
 android.permission.ACCESS_FINE_LOCATION
 android.permission.ACCESS_NETWORK_STATE
 android.permission.CAMERA
 android.permission.INSTANT_APP_FOREGROUND_SERVICE // only in Android 8.0.
 android.permission.INTERNET
 android.permission.READ_PHONE_NUMBERS. // This permission is available only in Android 8.0 (API level 26).
 android.permission.RECORD_AUDIO
 android.permission.VIBRATE
```





## Converting Application Module to Feature Module - Exhibit B

- **CodeLab** : https://codelabs.developers.google.com/codelabs/android-instant-apps/index.html


```
apply com.android.application -> com.android.feature
```

### 3-Modules
기존의 앱은 `com.android.application` gradle 설정을 사용한 single app module로 이루어져있다.
instant앱을 만들기 위해서는 base 모듈을 생성해야하며, 기존앱과 인스턴트앱이 base 모듈을 공유하는 형태로 구조 변화가 필요하다.

![modules](https://codelabs.developers.google.com/codelabs/android-instant-apps/img/732de5f26beedbff.png)

```
- com.android.feature : modules where the actual code for the app resides.
- com.android.application : module which builds your installable app. Does not need to contain app code. Consumes feature modules.
- com.android.instantapp : module which builds your instant app. Does not contain app code. Consumes feature modules.
```


### More and More seperate
1개의 base 코드위에 여러개의 feature를 두는 것도 가능하다. 마치 다음 그림처럼 말이다.

![Muliple Modules](https://codelabs.developers.google.com/codelabs/android-multi-feature-instant-app/img/fe90bb64322ffae0.png)



# Games on Google Play Instant Workshop

## Agenda
- Game Patterns Overview
- Required Build Changes
- Play Developer Console


## 1. Gamebase Patterns Overview
```
Upcoming game launches > Recently launched games > Late stage games
```



## Build Instant App
1. Update AndroidManifest.xml
    - android:targetSandboxVersion="2"       < <manifest />
    - android:targetSdkVersion="27"          < 최소 27, <uses-sdk />
2. Build project
3. Sideload to device


- unity plugin : http://github.com/google/play-instant-unity-plugin
    - 추후 Asset Store에서 다운로드 받을 수 있도록 작업 할 예정



### Command line
- https://developer.android.com/studio/#command-tools

```sh
$ tools/bin/sdkmanager 'extras;google;instantapps'
```