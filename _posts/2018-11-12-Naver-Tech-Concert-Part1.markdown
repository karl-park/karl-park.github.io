---
layout: post
title:  "[Android] Naver Tech Concert 2018 (Day2) - Part1"
date:   2018-11-12 09:00:00
tags: android seminar naver navertechconcert techconcert java kotlin
categories: devstory
---


##### 다른 세션들
[Naver Tech Concert 2018 (Day2) - Part1](/devstory/2018/11/12/Naver-Tech-Concert-Part1/) <br/>
[Naver Tech Concert 2018 (Day2) - Part2](/devstory/2018/11/12/Naver-Tech-Concert-Part2/) <br/>
[Naver Tech Concert 2018 (Day2) - Part3](/devstory/2018/11/12/Naver-Tech-Concert-Part3/) <br/>


# 1. 변화의 시대: 안드로이드 앱 어떻게 개발할 것인가?
> 신동길(네이버 앱개발)님의 발표내용을 바탕으로 재구성하였습니다.


지금은 바야흐로 안드로이드 개발에 있어 변화의 시대, 격변의 시대라 불리울만 합니다. 앱 개발에 있어, 많은 부분이 변화되었고 변화되고 있기 때문입니다.

Hardware가 우선 MultiCore와 Large Memory를 품게되었고, Big Display도 지원하게 되었습니다. 또한 Platform에 있어서는 Dalvik에서 JIT/ART 환경으로 변화되었습니다. 또한, 수많은 프레임워크들과 패턴들이 등장함으로써, 지금 안드로이드 개발환경은 가히 춘추전국시대라 불리울만큼 우후죽순한 프레임워크들로 북적댑니다. (Lottie, RxJava, Retrofit, Glide, Picasso, OkHttp, ......)

게다가 "Java" Language가 가지는 한계점, 적합성에 대한 의문제기는 계속됩니다.
Java는 "상속, 오버라이드" 구조와 Listener 표현법1-1이 코드를 장황하게 하는 단점들이 있습니다. 이로 인해 보다 간결한 코드(많은 기능이 제공되는 언어)를 작성하고싶은 니즈가 끊임없이 발생하고, Funtional Programing(이하 FP)의 패러다임을 받아들이는 방향으로 나아가고 있는 듯 합니다.

안드로이드 개발 방향 논의에 앞서, ***"안드로이드 앱의 구조"*** 에 대해 먼저 짚고 넘어가봅시다. (안드로이드의 구조를 훑어보면서, 어떤 구조들이 최선일까를 같이 고민해보면 좋겠습니다.)


## 안드로이드 앱의 구조
![1.png](/static/assets/img/posts/navertechconcert18/1-1.png)

안드로이드 앱은 Application위에 Android의 주요 컴포넌트들 및 Web Engine, Shared/Persistant Data, Networking 등의 컴포넌트들이 올라가있는 것으로 설명될 수 있습니다.


### UI 구조 - Activity, Window, Fragment, View
![2.png](/static/assets/img/posts/navertechconcert18/1-2.png)
안드로이드 프레임워크는 GUI 기반 구조입니다. Activity 구조, Fragment 구조, View 기반의 구조의 장단점은 무엇인가? 어떤 구조를 택할 것인가를 같이 고민해보며 안드로이드 개발을 했으면 합니다.


### Page Navigation
![3.png](/static/assets/img/posts/navertechconcert18/1-3.png)
요새 Android 진영에 부는 바람이 바로 Single Activity App입니다. 마치 Web Front단에서 SPA(Single Page Application)가 유행인(유행이었던) 것처럼, Android도 하나의 Activity에서 SubView들로 Page Navigation을 구현하는 방식이 실험되고 시도되고 있습니다.


## Event Dispatching
안드로이드의 Activity-View Hierachy는 매우 복잡합니다. 많은 수의 이벤트가 오고가며, View의 Depth는 깊어져서 sub-classing을 피할 수 없습니다. Activity의 event를 각 view에 전달해주어야할 필요가 많은데, 각각의 리스너를 따로 관리하는 것은 위에 언급한대로 너무나도 복잡합니다.

그래서 Naver에서는 다음과 같이 `instanceof` 연산자를 이용한 이벤트처리를 합니다.

```java
public class EventActivity extends Activity {
    Map callMap = new HashMap<Integer, Object>();
    List<Object> eventList = new LinkedList<>();

    @Override
    public void onBackPressed() {
        for (Object l : eventList) {
            if (l instanceof OnBackPressedListener) {
                if (((OnBackPressedListener) l).onBackPressed() == true) {
                    return;
                }
            }
        }

        super.onBackPressed();
    }

    public void startActivityForResult(Intent intent, int requestCode, OnActivityResultListener resultCallback) {
        callMap.put(requestCode, resultCallback);
        startActivityForResult(intent, requestCode);
    }

    @Override
    protected void onActivityResult(int requestCocde, int resultCode, Intent data) {
        Object l = callMap.get(requestCode);

        if (l != null && l instanceof OnActivityResultListener) {
            ((OnActivityResultListener) l).onActivityResult(requestCocde, resultCode, data);
        }

        super.onActivityResult(requestCocde, resultCode, data);
    }

    public void requestPermission(String[] permission, int requestCode, OnPermissionResultListener callback) {
        callMap.put(requestCode, callback);
        ActivityCompat.requestPermissions(this, persmission, requestCode);
    }
}
```

## Multi Process
멀티 프로세스는 다음과 같은 상황일 때 필요합니다.
- 메인과 구별되는 기능 요소가 필요할 때
- 별도의 모듈(Dynamic Linked Library)을 로드하는 요소가 필요할 때
- 모듈의 크기가 큰 경우
- 멀티 미디어와 같이 자원을 많이 소모하는 요소가 필요할 때
- 크래시 발생시 해당 모듈에서 전파되기를 원하지 않을 때

멀티프로세스의 사용을 위해서는 다음 작업이 필요합니다.

1) 별도의 Activity, Service 등 컴포넌트에 process 속성 지정 [참고](https://developer.android.com/guide/topics/manifest/application-element#proc)
   ```xml
    <activity android:process="{string}" />
    ```
2) 프로세스마다 Application.onCreate()에서 초기화
3) Content Provider, Intent Bundle, Intent Broadcast로 데이터 공유 (Parcel/Parcelable, Intent(Activity: startService, Service: sendBroadcase), Messenger

https://developer.android.com/guide/topics/manifest/activity-element


## 다양한 Architecture

### UI-Data Pattern
MV, MVC, MVP, MVVM, ......
정말 다양한 아키텍쳐가 쏟아져나오고 있고, 많은 개선 시도들과 협의들이 진행중입니다. 본인의 서비스에 알맞은 패턴을 도입하려면, 우선 많은 경험이 있어야 할 것입니다.

MVC, MVP, MVVM 등 패턴은 통일된 인터페이스를 가질 수 있지만, 코드의 양을 절대적으로 증가시키기도 합니다. 즉 작은 부분에 적용하면 오히려 배보다 배꼽이 큰 경우가 생기기도 합니다. 협업시 레이어 구분등을 위해 사용하면 좋은 성과를 낼 수 있지만, 단순한 구조에 적용하는 것에는 신중해야합니다.


#### MVC, MVP, MVVM

![4.png](/static/assets/img/posts/navertechconcert18/1-4.png)

![5.png](/static/assets/img/posts/navertechconcert18/1-5.png)

![6.png](/static/assets/img/posts/navertechconcert18/1-6.png)

<출처: [ream post](https://academy.realm.io/kr/posts/eric-maxwell-mvc-mvp-and-mvvm-on-android/)>

### 프레임워크
네이버앱은 Lottie, Retrofit, okhttp 등 정도만 프레임웤을 쓰고 있다고 합니다. 나머지는 Android Native로 순수 구현을 하려고 노력하고 있습니다.

그 이유로는 오픈소스를 쓰면 사용되는 기능에 비해 앱의 크기도 커지고, 오픈소스 버전업에 따라 대응점들이 많아지기 때문입니다.


### Event-Driven Pattern - State Model, FSM(Final State Machine)
State Model - 간단한 이벤트로 상태관리
FSM - 이벤트에 따른 상태변화와 동작 구조, 앱의 전체 동작관리




# 2. Efficient and Testable MVVM pattern
두번째 시간으로는 뱅크샐러드를 개발한 레이니스트에서 안드로이드 개발자로 일하고 있는 김범준님의 발표였습니다. MVVM 패턴을 왜 본인/회사에서 선택하였고, 어떻게 테스트 코드를 효과적으로 작성했는지를 다루는 발표였습니다.


## Why MVVM
MVC(~~Android 기본 아키텍쳐?!~~)를 벗어나, MVP의 시대가 한 때 왔었습니다.(-ing) 하지만 MVP의 구조를 적용하다보면, Presenter는 점점 방대해지고, 수천줄에 이르는 Presenter의 코드를 볼 수 있었습니다. 중복 코드가 늘어나고, Testable하지 않은 코드들과 보일러 플레이트 코드 양산 등, MVP 패턴 코드를 유지보수 하면서 겪은 어려움들이 많았습니다.

그래서 MVVM을 적용해보고 효율적이고, 테스트가 쉬운 구조를 만들어보며, 이번 시간 발표를 해봅니다.

MVVM은 다음과 같은 특징을 갖습니다.
- Model, View, ViewModel 로 이루어짐
- ViewModel은 View의 추상화
- View와 ViewModel은 n:m의 관계
- View는 ViewModel에 bindable함

MVVM에서 가장 중요한 것은 바로, "DataBindingUtils" 일 것입니다. 이에 관해서는 다음 아티클들을 참고해주세요.

- [정승욱님,  Android에서 MVVM으로 긴 여정을..](https://medium.com/@jsuch2362/android-에서-mvvm-으로-긴-여정을-82494151f312)
- [Wikipedia MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel)



## 우선은 Clean Architecture

![7.png](/static/assets/img/posts/navertechconcert18/1-7.png)

MVVM 은 Clean Architecture를 지향합니다. [마틴 파울러 형님의 Clean Architecture PDF](http://putregai.com/sbooks/clean_arch.pdf)

Clean Architecture는 각 레이어들간의 디펜던시를 명확하게 하는 설계로서, IoC를 기반으로 합니다.

IoC를 구현하기 위해 뱅크샐러드에서는 [Koin](https://github.com/InsertKoinIO/koin)이라는 프레임워크를 이용하였습니다.

Koin은 Service Locator 기반의 IoC 프레임워크입니다.


## Koin
- IoC를 구현할 수 있게 도와주는 Library
- Kotlin으로 구현
- 간편한 사용/AAC 지원
- IoC를 Service Locator로 지원함(DI 아님)
    - Runtime에서 에러확인
- 

## 예제 
git : https://github.com/omjoonkim/GitHubBrowserApp


## Spek
테스트는 Spek을 이용하여 합니다.
[Spek](https://github.com/spekframework/spek) 은 Kotlin을 위한 테스트 프레임워크 입니다. 마치 JavaScript에서 Jest 등으로 테스트를 할 때 처럼 테스트를 합니다.

예를 들면 다음과 같은 테스트 코드가 있다고 합시다.

```kotlin
class TestClass {
    @Test
    fun testCalculateTaxRate() {
        val calc = MyCalculator();
        val value = calc.sum(30, 7);
        assertEquals(37, value);
    }
}
```

위와 같은 코드는 "메소드명"을 보고 어떤 테스트를 하는지를 개발자가 "추정"해야하는 번거로움이 있습니다. 또한 해당 결과가 어떤 것을 의미하는 지도 추정을 해야하죠.

하지만 Spek을 사용하면 다음과 같이 나타낼 수 있습니다.

```kotlin
class TestClass : Spek({
    describe("계산기가 작동 잘하는지 테스트를 해봅시다.") {
        val calc = MyCalculator()
 
        it ("덧셈 테스트. 첫번째, 두번째 파라미터를 더한 값을 테스트.") {
            val sum = calc.sum(30, 7)
            assertEquals(37, sum)
        }
 
        it ("뺄셈 테스트. 첫번째 숫자에서 두번째 숫자를 빼는 것을 테스트.") {
            val sub = calc.subtract(14, 2)
            assertEquals(12, sub)
        }
    }
}
```

쉽고 간단하지요? Koin과 Spec을 사용하면 재밋는 개발이 가능 할 것 같습니다. 물론 MVVM 패턴으로 작성하면 디펜던시도 명확해지고 Testable한 코드를 작성하기 한결 쉬울 것 같네요.