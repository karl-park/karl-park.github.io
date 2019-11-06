---
layout: post
title:  "[Android] Google In-App Promotion and Play Points"
date:   2019-10-25 07:00:00
tags: android billing iab iap promotion playpoint google googleplay googlestore
categories: devstory
---
Google In-App Billing에는 **Promotion** 과 **Play Points** 라는 서비스가 존재한다.
본문에서는 각각의 기능을 간략하게 소개하고, 구현 방법을 다룬다.

# Google In-App Promotion, Play Points

### Google Play Promotion
**`Promotion`** 기능은 각 게임에서 소모성 아이템(Consumable)을 등록한 이후, **프로모션** 탭에서 프로모션 생성 및 특정 아이템을 프로모션으로 특정하여, 유저에게 "프로모션 코드" 제공 및 "아이템"을 **```무상 제공```** 할 수 있는 방법을 말한다.

### Google Play Points
이에 반해, **`Google Play Points`** 는 Google In-App 결제시 쌓이는 포인트들을 의미하며, 쌓여진 포인트로, In-App 아이템 등을 구매할 수 있는 시스템을 말한다. 해당 방법은 **```유상 구매```** 에 해당한다. 

<small>*(2019년 10월 현재 Korea, Japan 만 Google Play Points 프로그램이 활성화 되어있다.)*</small>

<br/>
두 방법 모두, 구현 방법에는 큰 차이가 없므으로, 아래에서는 각각 방법을 **설정** 하는 방법을 설명하고, **구현** 방법은 합쳐서 설명하도록 하겠다.

## 설정
### Google In-App Promotion 
1. 구글 플레이 콘솔에 접속하여, 인앱 상품을 생성한다. 
2. 사용자 획득 > 프로모션 > 새 프로모션 추가를 선택한다.
3. 프로모션 이름 및 시작일/종료일을 설정하고 프로모션 유형 및 프로모션 코드 수를 입력한다.
4. csv 파일로 다운로드 한 후, 프로모션 코드를 사용자에게 전달한다.

### Google Play Points
1. 구글 플레이 콘솔에 접속하여, 인앱 상품을 생성한다. 단, Google Play Points 기능은 Google Play 팀과 합의 한 후, 사용 할 수 있도록 한다.
2. 상품 설정시, Pricing 란에 **"Make this product available only in Google Play Points"** 를 선택한다.
    - ![googleplaypoints.png](/static/assets/img/posts/googleplaypoints/googleplaypoints.png)
    - Product ID 설정 시, **rew** sufix를 붙여서 Google Play 팀에서 Play Points 용 아이템임을 쉽게 확인 할 수 있도록 한다. (권장)

## 구현

In-App Promotion을 이용해 아이템을 구매한 경우와 Google Play Points로 아이템을 구매한 경우, 모두 Client 측에서의 구현 방법은 같다.

구현을 위해서는 **AIDL** 을 사용하는 방법과, **Google In-App Billing Library**를  사용하는 방법 2가지가 존재한다.

<small>*(AIDL을 사용하는 방법은 곧 Deprecated 된다고 가이드에 명시되어있으나, 널리 사용중이므로 참고하여 구현하도록 하자.)*</small>

### 1. AIDL 이용

AIDL은 Android Interface Definition Language의 acronym 으로서, 안드로이드 앱간 멀티 쓰레딩을 처리하기 위해 서로 다른 Process와 ICP를 수행하여  위해 사용된다. 자세한 사항은 다음 링크를 참고하자.

- [AIDL Guide](https://developer.android.com/guide/components/aidl) : https://developer.android.com/guide/components/aidl

여기서는 Google Play Billing Services의 몇몇 기능들을 구현하기 위해 AIDL을 사용한다.

> `주의`
>
> AIDL 을 사용한 Google Play Billing Services 사용은 deprecated 되었다. 사용시, 항상 API 삭제를 염두해보고 사용하자. 기 사용중인 프로젝트에 대해서는 다음 절에서 설명할 **Google Play Billing Libary** 으로의 교체/도입을 고려하길 바란다.


AIDL을 사용하기 위해서는 **.aidl** 파일에 AIDL 인터페이스를 정의하여, **src/** 하위에 저장해야한다. .aidl 파일을 빌드하면, Android SDK에서는 각각 파일에 대하여, **[IBinder](https://developer.android.com/reference/android/os/IBinder.html)** 인터페이스를 **gen/** 하위에 생성한다. AIDL을 사용할 서비스에서는 반드시 해당 **[IBinder](https://developer.android.com/reference/android/os/IBinder.html)** 인터페이스를 구현하여 사용해야한다.

<small>(본문에서는 **[com.android.vending.billing.IInAppBillingService.aidl](https://github.com/android/play-billing-samples/blob/master/TrivialDrive/app/src/main/aidl/com/android/vending/billing/IInAppBillingService.aidl)** 파일을 src/ 하위에 적절하게 저장 및 빌드 성공하여, IBinder 인터페이스를 생성하였다고 가정하고 진행한다.)</small>

AIDL을 사용하여 Promotion을 구현하기 위해서는 다음을 반드시 구현해야한다.
1. App이 start/resume 될 때 마다, **[getPurchases()](https://developer.android.com/google/play/billing/billing_reference.html#getPurchases)** API 를 호출해주어야한다.
    - onResume() 콜백에서 **[getPurchases()](https://developer.android.com/google/play/billing/billing_reference.html#getPurchases)** API 를 호출해주고 나서, 결제 프로세스를 수행한다.
2. App에서 dynamically하게 BroadcastReceiver 를 생성하여 등록해야한다.
    - onPause() 콜백에서 **unRegisterReceiver(myPromoReceiver)** 를 통해서 unregister 함으로써, 시스템 오버헤드를 줄인다.
    - BroadcastReceiver에서 onResume()을 통해 이벤트를 전달받으면, **[getPurchases()](https://developer.android.com/google/play/billing/billing_reference.html#getPurchases)** API 를 호출하여 위에서 설명한 프로세스를 진행한다.
    - **com.android.vending.billing.PURCHASES_UPDATED** intent를 수신하도록 한다.
    - 다음과 같은 코드로 등록한다.
```kotlin
val filter = IntentFilter("com.android.vending.billing.PURCHASES_UPDATED") 
```

### Google Play Billing Library 사용

1. App이 start/resume 될 때 마다, **[queryPurchases()](https://developer.android.com/reference/com/android/billingclient/api/BillingClient.html#queryPurchases)** API 를 호출해주어야한다.
    - onResume() 콜백에서  **[queryPurchases()](https://developer.android.com/reference/com/android/billingclient/api/BillingClient.html#queryPurchases)** API 를 호출해주고 나서, 결제 프로세스를 수행한다.
2. **[onPurchasesUpdated()](https://developer.android.com/reference/com/android/billingclient/api/PurchasesUpdatedListener.html#onpurchasesupdated)** 콜백을 구현한다.

## Reference
- Implementation of Promotion with AIDL: https://developer.android.com/google/play/billing/billing_promotions.html
- Implementation of Promotion with Google Play Billing Libary : https://developer.android.com/google/play/billing/billing_promo