---
layout: post
title:  "[Android] Google for Mobile I/O Recap 2018 Day2"
date:   2018-08-10 05:00:00
tags: java Android AndroidP Android9 GoogleI/O 2018
categories: devstory
---

# Developer Session

6/25일부터 26일, 양일간 열린 Google for Mobile I/O Recep 2018 Seoul(이하 Google I/O) 참가후기에 대한 글입니다.

이번 글에서는 [지난 시간의 글 - 키노트](https://nhnent.dooray.com/project/posts/2261715514282102815)에 이어서, Android P에 대한 주요 기능 소개가 다뤄집니다.

### Posts
- [Good-Information/103 Google for Mobile I/O Recap_001](dooray://1387695619080878080/tasks/2261715514282102815 "closed")
- [Good-Information/106 Google for Mobile I/O Recap_002](dooray://1387695619080878080/tasks/2261715727556268900 "closed")
- [Good-Information/104 Google for Mobile I/O Recap_003](dooray://1387695619080878080/tasks/2281128951586660690 "closed")
- [Good-Information/105 Google for Mobile I/O Recap_004](dooray://1387695619080878080/tasks/2261715871455186377 "closed")



# 1. Android P 최신 기능 소개

가장 Highlights 되는 부분은 바로 `Intelligent` (A smarter spartphone, with machine learning at the core)이다.

즉, 머신러닝을 적용해서 더욱 스마트한 스마트폰을 만든다는 것이 골자.
다음의 내용이 적용점이다.
1. Adaptive Battery
2. Adaptive Actions (`Deep link`)
    - Slices

## 1. Adaptive Battery
- **Multi-release effort to improve battery**
롤리팝 이후로, 계속해서 배터리 성능 향상을 위한 노력 중이다. 

How? -> `제약`의 추가

```
App Restriction -> More App Restriction -> More and More App Restriction
```

#### 3개의 꼭지
1. Device idle state (Doze)
    - Deffered Jobs/Syncs/Alarms/Restructed Network/Wake Locks
2. User control (Battery Saver)
    - Background Restrictions
3. App use recency
    - App Standby Buckets


#### Bettery를 위한 Android P 제약 사항
- App Standby Buckets
    - Active / Working Set / Frequent / Rare 의 Set으로 나눠놓음.
    - 제약이 걸리면 (Rare에 가까울 수록) 백그라운드 네트워크 사용이 막힘
    - Rare Set이면, High Priority FCM 도 제약이 걸린다.
    - ![App Standby Buckets](https://www.androidpolice.com/wp-content/uploads/2018/06/2018-06-11-21_02_48-9-Whats-new-in-Wear-OS-by-Google-Google-I_O-18-YouTube.png)
- App Restriction


### Android Vitals
Play Console 에서 볼 수 있다.

ANR rates/Crash rates/`Excessive wakeups`/`Stuck partial wake locks`
뒤의 2개의 경우를 잘 체크해보고, 배터리 사용량을 최대한 낮출 수 있도록 해야한다.
 
Bad behavior 앱으로 판명이 나면, Background job에 제약이 걸릴 수 있다.

- Excessive wakeups : 잠들어있는 앱을 계속해서 깨움(alarm등으로)
- Stuck partial wake locks : 잠들지 못하게 함



## 2. Adaptive Actions
Deep Link 라고 생각을 하면 쉽다. `actions.xml` 에 정의하여 사용을 한다.

참고 URL : https://developer.android.com/guide/actions/

### Built-in Intents
Google은 머신러닝을 통해서, 유저가 무엇을 하고 싶은지 알 수 있다. 하지만, 어떤 앱이 사용자의 Intent를 만족해줄지는 모른다. 그렇기에, Built-in Intents를 준비하여, Google에 알려주면 구글이 유저에게 알려줄 수 있다.

계속해서 Built-in Intents를 늘려갈 예정이다. 커스텀 인텐트도 지원하려고 노력중이다.

메타데이터 `actions.xml` 파일에 Intents를 정의하면 된다.

![image.png](/files/2281052122427805008)
< App Actions, 출처 구글 안드로이드 개발 사이트(위 참고 URL) >

하지만, 버튼으로만 제공하기 때문에 제한이 있다. 그래서, 단순한 버튼이 아니라, UI 자체를 앱 외부에 표현할 수 있도록 지원하는 것이 바로 `Slice`이다.


### Slice
`App Widget(RemoteView)` 이라는 개념과 사실 하고자 하는 일이 비슷하다. (앱 외부에 앱의 UI를 표현하고자 하는 목적)
하지만, RemoteView는 자유도가 높은 만큼 사용도가 매우 어려웠다. 또한 2008년에 나와서 너무... tooooooo old ! (`RemoteView is so 2008.`)

그래서 바로 Slice가 나왔다. Notification을 만드는 것과 비슷하다.(빌더를 이용해서 템플릿에 맞춰서 만들면 된다.)

`Support Library`에 포함되어있다 !!

ContentProvider와 비슷한 SliceProvider를 이용하면 된다.

결론은 Actions와 Slices를 결합해서 사용하면 매우 좋은 사용자 경험을 제공 할 수 있다.


## Notification
- MessagingStyle
    - Images/Stickers 등을 바로 Notification에 붙일 수 있음
    - People focuesd messages with individual avatars (people 2 people의 경우에는 마치 대화 처럼 Notification 활용도를 높임)
- Smart Replies
    - Android Wear에서 잘 쓰고 있었음
    - SuggestionChips를 제공하여, notification을 받았을 때, 특정 행동등을 할 수 있는 버튼을 달 수 있음
    - ML Kit 기반으로 제공될 예정
- Notification Blocking
    - 어떤 사용자가 특정 notification을 매번 닫는 것이 학습되면, Notification Helper(?)가 떠서, "이 알림을 꺼버릴까요?"라고 물어볼 것임.
    - Notification Channel 을 활용해야한다. Notification을 세분화하지 않으면(Channel) 전체 알람이 블록 당할 수 있다. 채널을 다분화 하여 해당 채널만 Blocking되도록 잘 나눠야한다.


## Display Cutout
- Cutouts in the Ecosystem
    - 노치를 지원함 !!! (이미 시장에 노치를 지원하는 기기들이 많아지고 있음. 2018년에만 30개의 디바이스가 예정)
- `Hard-Coding Status Bar` 일 경우 문제가 될 수 있다
    - 노치가 있기 전에는 24dp 였는데, 노치가 있다면 53dp..
    - 그럼 어떻게 ?
        - 일반적인 activity : 하드코딩된 status바 높이가 없다면 그대로 사용하면 됨(Android framework에서 자동 계산해서 nav bar 등을 그려줌)
        - 풀스크린 : regular activity -> full screen으로 왔다갔다하는 앱의 경우 전체 window의 크기가 작아질 수 있기 때문에 레이아웃이 다 깨질 수 있다. 확인해봐야한다.
        - Cutout Aware Display
            - WindowInsets.getDisplayCutout() 을 이용하여 노치 위치에 컴포넌트를 위치 할 수 있다.




## Private API 금지됨
hidden, hide 등으로 숨겨놓은 private API의 경우에는 이제는 리플렉션 등으로도 실행이 안되도록 막아놓았다. 이러한 금지 배경에는 `public API만으로도 사용이 가능해아한다.`라는 철칙이 있다.

- API Categories
    - Whitelist : 사용가능
    - Light greylist
    - Dark greylist : 최신에는 대체제가 있음. 구버전에는 없음
    - Blacklist : 플랫폼에서 사용하는 API라서 사용 불가능



## Google Play Target SDK Rules
targetSDKVersion 26 이상으로 빌드 및 출시 되어야한다. (올 11월까지 모든앱 대상)


## ChromeOS
- Linux 기반 앱을 돌릴 수 있도록 업데이트 되었다.
- Android Studio on ChromeOS 가 되도록 지금 테스트 및 작업중이다.(크롬 OS 가 잘되면 크롬북을 하나 장만해봐야겠다.)



# 2. 새로운 모듈 Android App Bundle로 앱 개발하기

![image.png](/files/2281058518223198458)


참고 URL
    - https://developer.android.com/platform/technology/app-bundle/
    - https://developer.android.com/guide/app-bundle/


App Bundle : App Packing을 조금 더 경제적으로 할 수 있는 방법 ! 적은 용량으로 다양한 기기 대응을 가능하게 해준다.

![App-Bundle-Animation_GIF.gif](/files/2281063564271415842)
< Android App Bundle,  출처 : 구글 >

## Format of android app bundle

```
android app bundle !== APK
```

Publishing format
- Not installable
- Metadata fiels
- Strict Validation


```
- base
    - manifest
    - resources.pb
    - native.pb
    - assets.pb
    - ...
```


## File Targeting
- the right files for the right users / devices
```
- Language
- Texture compression format(soon)
- Graphics API version, for OpenGL & Vulkan(soon)
```

## Serving
Split APKs(Introduced in Android Lollipop, Multiple installed for a single app, Appear as if a single APK)

- Split APK Format : Format is same as an APK, Same package name & version code

```
App bundle -> Base APK (different split APK files, graphical, architecture, languages)
```


## Size Savings
- 20% 평균 감소
- 만약 모든 앱이 App Bundle을 적용한다면 10Pb per day의 절감효과 !!


## Signed and ready for publishing
- Splits : No configuration necessary

```
bundle {
    density {
        enableSplit = false
    }

    abi {
        enableSplit = false
    }

    language {
        enableSplit = false
    }
}

```

Q. App Bundle 관련 질문 
    만약 유저가 "한글" 설정한 앱을 다운로드 받고 사용중에 만약 "다른 언어"로 바꿨을 경우, 앱을 다시 받아야하는 것인가요?
A.Yes, 해당 언어셋에 대한 apk만 다운로드 받도록 처리함


## Enrolling app to play console
- release key
- Google play App Signing


## Bundle Explorer in Play Console
여기에서 특정 apk(각 환경에 따른)의 다운로드 수, saved file size, apk 파일 등을 다운로드 및 확인 할 수 있다.

## How to test an Android App Bundle
1. Internal test track
2. Bundletool : testing locally with high fidelity
    - AAB -- build-apks -> APK Set Archive(.apks) -- install-apks -> device
    - Bundletool : testing with CI
```
$ bundletool build-apks \
    --bundle=myapp.aab \
    --output=myapp.apks \
    --device-spec=pixel2.json \
    --ks=/tmp/keystore.jks
$ bundletool install-apks \
    --apks=my-app.apks
```

universal apk
```
$ bundletool build-apks \
    --bundle=myapp.aab \
    --output=myapp.apks \
    --device-spec=pixel2.json \
    --ks=/tmp/keystore.jks
    --universal
```


- Reference : https://github.com/google/bundletool


## Modularization
When to Modularize ?
- How many users? How big? Can users wait?

고려해볼 만 하다. APK에 각 기능들을 모듈화 하는 것을 넘어서, 사용자가 해당 기능 사용 여부를 판단하여 플러그인을 붙이는 형식으로 다운로드 받게 하는 것은 사용자 경험상 매우 좋을 것 같다.

- Reference : https://g.co/androidappbundle







# 3. 빠르고 세련된 Android 개발 - Jetpack & Android Studio

## Layout
ListView가 얼마나 쓰기 힘들었는지 알고있다. 이를 대체하려고 RecyclerView가 나왔고, 이러한 연장선 상에 Constraints Layout, Arch Components 등이 계속 나오고 있다. 또한, Kotlin, Studio Profilers, Ktx(Kotlin Android Extension), Paging 등등이 계속 나오고 있다.

Xcode처럼, Android Studio의 `Layout Inspector` 가 나왔다 !! (Examine View Hierarchy 라는 고대의 유물을 더 이상 쓸 필요없음 !)

LayoutDesign이 xml을 통해서가 아니라 UI tool을 통해서 레이아웃을 짜는 것이 더 쉽도록 지원된다.

![image.png](/files/2281065109544535552)
<Layout Inspector, 출처: 구글>


## Profiler
Profiler 또한, Android Studio에 DDMS가 아니라, Profiler를 실행하면 된다 !!!


## Memory Tracking
메모리 트래킹을 하는 것은 좋은데, 데이터를 뽑는데 시간이 많이 걸리고, 그 덤프를 비교 및 분석하는것이 어렵다 !
Dalvik Debug Monitor => Android Profiler 내에 있다 !

덤프를 떠도 되지만, Memory Tracker를 통해 실시간으로 메모리 할당상태를 볼 수 있다.



## Runtime & Language

### Dalvik
- Optimized for size, 사이즈에 최적화가 되어있다. 처음 Dalvik이 나왔을 때는 64Mb만 어플리케이션에서 점유 할 수 있었다. 그렇기에 "저성능 단말기에서 효율적으로 동작하는 런타임"이 필요했다.

하지만 Dalvik은 유지되면서, 하드웨어/소프트웨어 발전이 이뤄지다 보니, 많은 문제들이 생겼다. 문제들은 다음과 같다.

features
- JIT optimizations not as powerful
- Allocation/collection slow
- Heap fragmentations
    - 파편화된 메모리를 GC전까지는 제어할 방법이 없다.
    - enum을 쓰지말고, int 등을 써라... 라는 가이드라인이 이것때문에 나왔다.


### ART (Android Runtime)
- `ART`가 나왔으므로, Dalvik은 안녕

- **Features**
    - JIT + AOT
    - Faster allocation/collection
    - Heap defragmentation
    - Large object heap


## Java Programming Language
Java를 Android가 처음 채택하여 발전하였다.
(Java를 할 수 있는 인력 풀이 상대적으로 컸기에Java를 채택한 것이 아닐까?)

- **Features**
    - Started with the Java 1.5
    - Extreamely popular
    - great tools
    - slow adaption of new versions


But.... 정말 이대로 괜찮은가? (의 대답은 역시 kotlin)

## Kotlin
Official support in 2017. Close collaboration with jetbrains.
Many great features that make code more enjoyable to write & read.

- https://github.com/android/kotlin-guides

코틀린해라. 당장해라. 안할 이유가 없는데 왜 안하냐 !!!!!!!
(도입이 너무 늦으면, 도입하는 회사로 이직을 해라. - 공감)


## Layouts
| Layout                                | 쓸만한가요?                    | 써도 괜찮은가요? |
| -------------------------------- | ----------------------------- | - |
| AbsoluteLayout                 | So easy!                         | Do not use !!! Never ! (픽셀기반이라, 해상도가 다양해진 경우에 대응 불가) |
| LinearLayout                      | Infinitely nestable!        | Ok for simple cases |
| FrameLayout                     | It's Fine!                         | Okay Good |
| GridLayout                        | Complexitastic               | Better with tools (which don't exist) |
| RelativeLayout                  | Relatively Expensive       | See below |
| `ConstraintsLayout`          | The answer!                    | 2.0 출시예정. 이걸로 쓰세용! |



## AdapterViews
(ListView, GridView, Gallary, ...)

ViewHolder Pattern 등을 써서 ViewHolder 클래스 등을 다 구현해야함..

- Challenges
    - Coarse-grained changes
    - ViewHolder
    - Animations


## RecyclerView (AdapterViews의 대안)
LayoutManager, ViewHolder 패턴의 의무 사용, Item에 대한 뷰 변경이나 애니메이션 개념이 추가되어서, ListView의 성능은 보완하면서도 사용성도 올려준다.

ListView는 수직 스크롤만 가능하다.(물론 가능케하는 방법이 있긴하다) 하지만 RecyclerView에서는 LayoutManager등을 통해서 orientation, gridlayout 등을 쉽게 구현 할 수 있다.

```java
mActivity = getActivity(); mRecyclerView = (RecyclerView) mActivity.findViewById(R.id.recycler_view); mLinearLayoutManager = new LinearLayoutManager(mActivity.getApplicationContext()); mRecyclerView.setLayoutManager(mLinearLayoutManager);
```


- **Features**
    - Android 5.0(롤리팝, API 21)
    - notifyItemInserted(position)
    - notifyItemChanged(position)
    - notifyItemRemoved(position)


- 알아두면 좋을 점
    - ViewHolder 패턴을 사용한 ListView와 RecycleView의 성능은 거의 같다. 즉 RecycleView에서는 ViewHolder 패턴을 강제한 것 의미 정도를 가진다.
    - 더 중요한점은 ViewHolder 패턴이 가져오는 급격한 성능 향상은 "저성능 폰"에 국한된다는 것이다. 최신 폰일수록 패턴 사용 여부와는 크게 관계없는 성능을 가진다.





## Fragments
Complicated.
`Core Platform dependency (버그가 있으면 API 버전을 높여야함)`

대안은? -> ```See Below !```


## Support Library Fragments
Core platform API @Deprecated
Use support library framgemnts!

`Support Library에 있는 Fragment 사용 권장`

- android.app.Fragment (~~권장~~)
- android.support.v4.app.Fragment (권장)



## Activities...
Android Apps consist of many activities

***Use single activities when possible(One per entry point)***

하나의 액티비티를 바탕으로 프레그먼트 등을 이용하여 화면을 전환하는 것을 권장하고 있다.

`가능하다면 싱글 액티비티 권장`



## Architecture
공식적으로 Architecture에 대한 가이드라인은 없었음
지금은 Architecture Components가 나왔음. 가이드를 참고하면 됨 !!
`Architecture Components`

URL : https://developer.android.com/topic/libraries/architecture/


## Handling Android Lifecycle
Android의 Lifecycle 너무나 복잡하다. (Activity Lifecycle + Fragment Lifecycle 이 합쳐지면 너무나도 복잡해짐)

Callback method를 통해서 처리를 하다보니 Activity, Fragment가 계속해서 무거워지게 되는데, 다음의 `Architecture Components: Lifecycle`을 이용하거나 LifecycleOwner를 써서 보다 쉽게 관리 할 수 있다 !!

- AAC : https://developer.android.com/topic/libraries/architecture/


## Views and Data
ViewModel !
Room ! (database)


## Android Jetpack
To build a modern android app

- Guidance
- Recommended libs and tools

![Android Jetpack](https://eluminoustechnologies.com/blog/wp-content/uploads/2018/06/36.png)



## ANDROID X
strict semantic versioning, 버전간 호환성을 제공

`androidx.<feature>.ClassName`

removed -v8, -v4, etc. naming


### Migration to Androidx
Android Studio > Refactor > Refactor to AndroidX


### AAR & JAR
Binary Dependencies -> `Jetifier: binary migration tool`
- `available now via google maven`



# 4. Start with Kotlin
더 이상의 설명은 생략. 바로 쓰자.
(기존 Java소스와 100% 호환. 러닝커브는 들지만, 지금은 당연히 해야할 때 !)





=============================
---


# AI & Cloud Session

# 1. Dialogflow와 ML API를 활용한 챗봇 개발
SNS를 넘어선 메신저 사용자 !

messenger기반의 챗봇은 양방향 기반이기 때문에 날로 그 중요성은 높아질 것이다.


## 3가지 주요 사용 사례
- Customer Suppport (CS)
- Transactions
- Getting things done


## How to
- `Intent` 파악
- `Entity` 파악
- `Context` 참조

```
ex) I like to eat banana

intent : eat sth.
entity : banana

ex) How many calories are there?
A: There are 89 calories in a banana.

context: communication
```


Chatbot + ML API + Google Assistant

앞단의 UI는 Google Assistant 를 사용함
영단어는 Google Sheets에 넣었음
Translation API 문장 번역 기능


## Dialogflow
챗봇계의 표준처럼 쓰이고 있음
Google Assistant와 연동하면 너무 좋다.
백엔드 역할을 Dialogflow가 함.



## 예제 챗봇
- https://github.com/javalove93/dialogflow-mlapi


## Deep neural network
- DNN 이 나오기 전에는 모든 규칙을 다 입력을 해야함
    - 그래서 규칙 기반의 Chatbot 은 한계가 있다.
- 그만큼 학습이 매우 중요하다.


## Google ML API
- TensorFlow
- Cloud ML
    - Custom Models
    - TensorFlow를 여러개 돌릴 수 있는 Paas 형태의 cloud 플랫폼
- Pre-built Models



## 자연어 처리 Natural Language API
감정 분석(Sentimental), Syntax, Entities, Categories 등 분석이 가능


## 음성 인식 API Speech API
음성을 인식 Text로 변환.
구문힌트나 인식모드 등을 통해 정확도 향상
(전화통화 등 다자간 대화, 동영상 내부의 음성을 데이터셋으로 학습을 시킴)


## 사진 분석, 텍스트 인식 Vision API
사진을 분석해서 Entity, Text, 사람 및 표정, 장소, 부적절한 이미지 등을 분석함.



## Video Intelligence API
영상을 분석해서 entity를 추출하고 레이블과 해당 내용이 나온 장면 등을 분석

- https://cloud.google.com/translate
- https://cloud.google.com/natural-language
- https://cloud.google.com/speech
- https://cloud.google.com/vision








# 2. 모바일 개발자를 위한 TensorFlow Lite 소개

TensorFlow Lite : TensorFlow의 Mobile 버전

머신러닝이 왜 모바일 기기에 중요할까?

Search Maps Gmail Chrome Android Assistance .. 이미 많은 곳에 ML이 쓰이고 있음

It is optimized for device


- tensorflow : https://www.tensorflow.org/
- tensorflow lite : https://www.tensorflow.org/mobile/tflite/



## Google Inception Model 잘 설명해놓은 블로그
- https://norman3.github.io/papers/docs/google_inception.html

## Hardware Acceleration
Andorid : OpenGL
iOS : Metal


## How do i use tensorflow lite
Get a Model : download or train
Convert : the model to tensorflow lite
Write ops : if needed
Write app : (use client API)



## Limitations on model design
Currently limited to inference ops
~50 commonly used operations uspported
Supports MobileNet, InceptionV3, ResNet50, SqueezeNet, DenseNet, InceptionV4, SmartReply and others
Quantized versions of MobileNet, InceptionV3
Extensible design allows using 'custom defined' ops




## Conversion tips
Be sure to use a frozen graphdef (or SavedModel)
Avoid unsupported operators
Use visualizers to understand odel (TensorBoard & TensorFlow Lite Visualizer)




# 3. 모바일 개발자를 위한 ML Kit: Machine Learning SDK 소개
ML Kit : Machine Learning SDK 소개

올해 TPU 3.0 공개함. 현재 외부에서도 사용할 수 있음(직접 접근은 안되고, 제공하는 API를 이용하여 접근가능)


## ONNX (Open Neural Network Exchange)
- Caffe2 + PyTorch
- 공통된 규격

## MLMODEL (Code ML model, Machine Learning Model)
CoreML2 제공함


## ML Kit
Google's Machine Learning SDK

Tensorflow 를 쓰려면, Tensorflow의 스펙을 모두 파악해야하는 어려움이 있다.
그리고, 트레이닝을 해서 모델을 만들면 좋은 모델이면 400Mb~1Gb 까지 하는 데이터크기를 모바일에서는 감당할 수 없다. 결국은 추론 밖에 없는데 어떻게 연동을 해야할지가 막막할 수 있다.

그것을 바로 `MLKit`을 이용하여 해결한다.


Firebase 로 프로젝트 생성해서 바로 써볼 수 있다.
랜드마크 검색같은 경우에는 cloud 기반이고 나머지는 (얼굴인식, 텍스트인식, 바코드인식 등) serverless로 바로 실행 해 볼 수 있다.

SmartReply API, 고성능 얼굴 칸토어 인식은 출시 예정인 API이다.