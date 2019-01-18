---
layout: post
title:  "[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기"
date:   2019-01-17 09:00:00
tags: kotlin java android safetynet google security verifyApps
categories: devstory
---

##### 다른 세션들
[[Android] SafetyNet Overview : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SsafetyNet-Overview/) <br/>
[[Android] SafetyNet Attestation API : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SsafetyNet-Attestation/) <br/>
[[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-SafeBrowsing/) <br/>
[[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-reCAPTCHA/)  <br/>
[[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기](/devstory/2019/01/17/Android-SsafetyNet-VerifyApps/)


# SafetyNet Verify Apps API
SafetyNet Verify Apps API는 디바이스의 다른 잠재적인 위험요소가 있는 앱들로부터 설정된 앱을 보호할 수 있습니다.

> [SafetyNet Attestation API](https://developer.android.com/training/safetynet/attestation.html)와는 기능이 다릅니다. SafetyNet Attestation API는 기기의 integrity를 체크하는 반면, SafetyNet Verify Apps API는 잠재적 위험이 있는 앱들이 설치되어있는지를 검사합니다.
> [SafetyNet Attestation API](https://developer.android.com/training/safetynet/attestation.html) 호출 이후 SafetyNet Verify Apps API 를 사용하시길 바랍니다.



만약, 여러분의 앱이 결제 정보나 민감한 유저 데이터를 다룬다면, 해당 기기가 다른 악성 앱으로부터 안전한지를 체크해야합니다. 만약, 안전하지 않다면, 민감한 데이터를 다루지 않도록 해야할 것입니다.



## 1. App Verification 활성화하기
SafetyNet Verify Apps API는 [Verify Apps](https://support.google.com/accounts/answer/2812853)를 활성화하기 위한 방법으로 2가지 메소드를 제공합니다. **isVefityAppsEnabled()** 메소드를 사용하여, app verificatino이 활성화 되어있는지를 판별 할 수 있습니다. 그리고 **enableVerifyApps()** API를 사용하여, app verification을 활성화 시킬 수 있습니다.


**isVefityAppsEnabled()** 메소드는 [Verify Apps](https://support.google.com/accounts/answer/2812853)의 현재 상태를 알려주는 반면, **enableVerifyApps()** 메소드는 해당 기능을 사용할지를 명확하게 유저에게 물어봅니다.


### 1) App Verification이 활성화 되어있는지 확인
**isVefityAppsEnabled()** 메소드는 비동기로, 유저가 [Verify Apps](https://support.google.com/accounts/answer/2812853) 기능을 사용하는지를 리턴해줍니다. **VerifyAppsUserResult** 반환값으로 유저가 그것을 사용했는지 등의 정보들을 내려줍니다.

```kotlin
SafetyNet.getClient(this)
        .isVerifyAppsEnabled
        .addOnCompleteListener { task ->
            if (task.isSuccessful) {
                if (task.result.isVerifyAppsEnabled) {
                    Log.d("MY_APP_TAG", "The Verify Apps feature is enabled.")
                } else {
                    Log.d("MY_APP_TAG", "The Verify Apps feature is disabled.")
                }
            } else {
                Log.e("MY_APP_TAG", "A general error occurred.")
            }
        }
```



### 2) App Verification 활성화 요청하기
**enableVerifyApps()** 메소드 또한 비동기로써, 유저에게 [Verify Apps](https://support.google.com/accounts/answer/2812853) 기능을 사용하도록 하는 Dialog를 노출시켜줍니다. **VerifyAppsUserResult** 반환값으로 해당 결과 등의 정보를 내려줍니다.

```kotlin
SafetyNet.getClient(this)
        .enableVerifyApps()
        .addOnCompleteListener { task ->
            if (task.isSuccessful) {
                if (task.result.isVerifyAppsEnabled) {
                    Log.d("MY_APP_TAG", "The user gave consent to enable the Verify Apps feature.")
                } else {
                    Log.d(
                            "MY_APP_TAG",
                            "The user didn't give consent to enable the Verify Apps feature."
                    )
                }
            } else {
                Log.e("MY_APP_TAG", "A general error occurred.")
            }
        }


```

## 2. 잠재적인 악성 설치 앱 리스트 얻기
**listHarmfulApps()** 메소드를 통해서, 알려진 (잠재적인) 악성 앱의 리스트를 받을 수 있습니다. 

> [중요]
> Google Play Store에서 활성화 되어있는 유저/계정에 연동된 앱의 리스트만 받을 수 있습니다.
> 

다음 API를 이용해서 목록을 얻을 수 있습니다.

```kotlin
SafetyNet.getClient(this)
        .listHarmfulApps()
        .addOnCompleteListener { task ->
            Log.d(TAG, "Received listHarmfulApps() result")

            if (task.isSuccessful) {
                val result = task.result
                val scanTimeMs = result.lastScanTimeMs

                val appList = result.harmfulAppsList
                if (appList?.isNotEmpty() == true) {
                    Log.e("MY_APP_TAG", "Potentially harmful apps are installed!")

                    for (harmfulApp in appList) {
                        Log.e("MY_APP_TAG", "Information about a harmful app:")
                        Log.e("MY_APP_TAG", "  APK: ${harmfulApp.apkPackageName}")
                        Log.e("MY_APP_TAG", "  SHA-256: ${harmfulApp.apkSha256}")

                        // Categories are defined in VerifyAppsConstants.
                        Log.e("MY_APP_TAG", "  Category: ${harmfulApp.apkCategory}")
                    }
                } else {
                    Log.d("MY_APP_TAG", "There are no known potentially harmful apps installed.")
                }
            } else {
                Log.d(
                        "MY_APP_TAG",
                        "An error occurred. Call isVerifyAppsEnabled() to ensure that the user "
                                + "has consented."
                )
            }
        }
```