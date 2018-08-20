---
layout: post
title:  "[Android] Google for Mobile I/O Recap 2018 Day1"
date:   2018-08-10 03:00:00
tags: java Android AndroidP Android9 GoogleI/O 2018
categories: devstory
---

# Google for Mobile I/O Recap 2018 Seoul

6/25일부터 26일, 양일간 열린 Google for Mobile I/O Recep 2018 Seoul(이하 Google I/O) 참가후기에 대한 글입니다.

이번 글에서는 그 첫시간으로 6월 25일에 열렸던 Google I/O 키노트를 정리해보겠습니다.

### Posts
- [Good-Information/103 Google for Mobile I/O Recap_001](dooray://1387695619080878080/tasks/2261715514282102815 "closed")
- [Good-Information/106 Google for Mobile I/O Recap_002](dooray://1387695619080878080/tasks/2261715727556268900 "closed")
- [Good-Information/104 Google for Mobile I/O Recap_003](dooray://1387695619080878080/tasks/2281128951586660690 "closed")
- [Good-Information/105 Google for Mobile I/O Recap_004](dooray://1387695619080878080/tasks/2261715871455186377 "closed")


# Keynote
## 환영사
- 발표자 : 민경환 (한국 안드로이드 앱/게임 비즈니스 개발 총괄)

이번주, AI week w/ Google가 열린다. CEO의 keynote등에서도 이미 들으셨다시피 AI에 대한 Google의 관심은 엄청나다.

AI/ML에 기반한 제품을 더욱 쓰기 쉽게 만들기 위해 노력하고 있다. Android에 포커싱하는 행사이지만, AI/ML에 대해서도 많은 관심 바란다.


# Google I/O 2018 최신 기술 총정리
- 발표자 : Tian Lim: Vice President of UX & Product for Play, Google
    닌텐도, Xbox 등 게임콘솔 개발에 참여하다 이제는 구글에서 일하고 있음.
    세상을 좀 더 편하게 만들기 위해 힘쓰고 있다. 구글은 엄청 빠른 생태계 변화를 가지고 있는 것 같다.

소규모 기업들, 교육, 게임 등 수많은 분야에서 변화를 주는 것이 "기술", 구글의 목표이다.

올해 Android 탄생 10년째.

## Android의 현재 지표
- active devices(20억) : 2B+
- apps downloaded daily 250M+
- country: 215+
- 신흥시장에서 android 개발자가 3배 이상 늘어남
- google subscribers : 구글 플레이 스토어 80% 이상 증가
- 게임 설치 유저수 : 2배 이상 증가



## #IMakeApps Hashtag Movement
Artist&AppDev 
ex) bemyeye

작년 구글 플레이 출시 앱 증가분이 43%가 넘었음
(안드로이드 앱을 누구나가 만들 수 있다는 운동.. 인데 할머니도 앱을 만드시고, 아이들도 만들고.. 난 이제 뭘 먹고 사나...)


## Google I/O 축제
sth like festival
Google I/O 는 단순한 기술 컨퍼런스를 넘어서서, 축제에 근접하고 있음 ~~(과연?)~~
- 7000+ participants
- 100 announcements
- 200 session videos

## Keywords
Google CEO 기조연설 키워드
1. AI For Everyone
    - Google Photos 등에도 AI에 적용되어 있음. (Suggestion Action, Color Pop ...)
    - Adaptive Battery
        - 화면 밝기를 사용자가 조절하는 데이터를 학습하여, 화면 밝기 자동 조절
        - 사용하는 앱/사용하지 않는 앱들을 학습
    - ML Kit
        - Text recognition, Face detection, Barcode scanning, Image labeling ...
        - TPU 3.0
            - Tensor Process Unit : Tensorflow 이용
                - 차세대 CPU
    - Healthcare
        - Helping diagnose diabetic retinopathy
        - 당뇨증 합병증인 망막변증 등을 진단을 할 수 있도록 도움을 주고 있음.
        - 얼마전에 기사에 나온, 95% 이상의 사망 위험 진단 인공지능이 생각이 났다.
    - URL : ai.google/principles
2. Digital Well-being
    - 디지털이 우리 삶을 매우 편하게 만들지만, 반면 삶을 매우 디지털에 의존적으로 변하기도 한다.
    - Dashboard/App Timer/Wind Down 등을 이용하여 디지털 디펜던시를 줄이고자 한다.
    - 플레이 스토어에 Stand Out Well-Being Apps 이라는 카테고리를 만듦 (뛰어난 웰빙앱)


## I/O Keynotes
- Android App Bundle
    - 평균 20% 이상의 apk size가 줄어듦
    - iOS의 App Thining과 비슷함(or bitcode)


## 모바일 개발자를 위한 I/O 2018: Android의 새로운 기능 소개
- India, Brazil, Korea 등과 같이 급격히 성장하는 시장
- 3 point of android topic
    - Innovative distribution
        - 컨버전 rate가 apk file size에 반비례함
        - **Dynamic Delivery**
        - 아래의 Google Play Instant for game developers 참고
    - faster development
        - AAC (Android Architecture Components)
        - Android Jetpack (Kotlin과 같이 사용하는 것을 목표로 개발했음, 95% 이상의 기기에서 작동함)
            - Architecture
            - Behavior
            - UI
            - Foundation
            - url : https://go.co/androidjetpack
    - increased engagement
        - Android Slices : 유용하고, 풍부한 템플릿들이 들어가 있음.
            - https://g.co/slices
        - App Actions
            - actions.xml 파일을 추가하면 됨.
            - 런처 뿐만 아니라, google search, smart selection, assistant 등에 나타남
            - 앱 밖에서 앱 안으로 인입을 시킬 수 있음 !!!!!
- Kotlin의 성장
    - 35% 의 pro개발자들이 kotlin을 사용 중 !!!!!!!!
    - Do it now !!!!!!

## Google Play Instant for game developers
- URL : https://g.co/instantapps
- 2017 Google I/O에서 instant app을 발표함
- 4Mb -> 10Mb 로 instant app size 상향
- Enabled progressive download
- Added game engine, NDK and OpenGL ES support
- `Unity plug-in comming soon`
    - Unity beta program : available now
    - https://g.co/play/instantbeta

- Adding instnat app build target to CocosCreator IDE
- Alpha build with instant app support
    - available now
    - https://goo.gle/Q3PnpL


## State of Kotlin
- 2017년 Kotlin의 급격한 성장세
- KotlinConf 2017
- LOC(Lines of code) => 급격한 성장세 (github)

- 48% switched completely to Kotlin from other languages.
- 20% work in companies with more than 5000 employees
- 2% Kotlin is their first programming language

Types of Applicatinos
- Mobile : ~= 60%
- WebBackend : ~= 50%
- second most loved language : stackoverflow (1st ? => 찾아보세용)
- Kotlin
    - Kotlin/JVM
    - Kotlin/JS
    - Kotlin/Native
        - server, desktop, embedded
        - macOS, windows, Linux, Raspberry Pi