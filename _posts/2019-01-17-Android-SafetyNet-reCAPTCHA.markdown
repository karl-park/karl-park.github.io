---
layout: post
title:  "[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기"
date:   2019-01-17 09:00:00
tags: kotlin java android safetynet google security recaptcha captcha
categories: devstory
---

##### 다른 세션들
[[Android] SafetyNet Overview : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SsafetyNet-Overview/) <br/>
[[Android] SafetyNet Attestation API : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SsafetyNet-Attestation/) <br/>
[[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-SafeBrowsing/) <br/>
[[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-reCAPTCHA/)  <br/>
[[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기](/devstory/2019/01/17/Android-SsafetyNet-VerifyApps/)


# SafetyNet reCAPTCHA API
reCAPTCHA API는 advanced한 위험 분석 엔진을 이용하여 스팸이나 어뷰징 행동들로부터 앱을 보호할 수 있는 무료 서비스입니다. 만약, 앱에 대해 유저 인터랙팅이 사람이 아니라 봇으로 판단된다면, 앱을 계속해서 사용하려면, 사람이 풀 수 있는 CAPTCHA를 띄웁니다.

> minSdkVersion이 14 이상이어야합니다.


## Prerequisite

### reCAPTCHA key pair 등록하기
[reCAPTCHA Android signup site](https://g.co/recaptcha/androidsignup)에 들어가서 다음의 단계를 따릅니다.
1. 아래 주소에 들어가서 다음 정보를 입력합니다.
    - [reCAPTCHA Android signup site](https://g.co/recaptcha/androidsignup)
    - **Label** : key에 대한 unique한 label 입니다. 주로, 회사나 조직이름을 사용합니다.
    - **Package Names** : 각 앱에 대한 패키지명압니다.
    - **Send alerts to owners** : 만약 reCAPTCHA API에 대한 이메일을 받고 싶다면 체크합니다.
2. **Accept the reCAPTCHA Terms of Service**를 체크하고 **Regiseter**버튼을 눌러 등록합니다.
3. **Adding reCAPTCHA to your app** 섹션에서 **Site key**와 **Secret key**가 각각 노출됩니다.
    - **Site key** : [검증 요청](https://developer.android.com/training/safetynet/recaptcha#send-request)에 사용됩니다.
    - **Secret key** : [유저 Response Token을 검증](https://developer.android.com/training/safetynet/recaptcha#validate-response)하는데 사용됩니다.

### SafeNet API Dependency 설정

SafeNet API 사용을 위해서 다음과 같이 디펜던시를 설정해줍니다.
더 자세한 정보는 다음 [링크(Set Up Google Play Service)](https://developers.google.com/android/guides/setup)를 참고해주세요.
```gradle
apply plugin: 'com.android.application'
...
dependencies {
    compile 'com.google.android.gms:play-services-safetynet:16.0.0'
}
```


## 1. reCAPTCHA API 사용

### 1) Verify Request 보내기
reCAPTCHA API 사용을 위해서는 [verifyWithRecaptcha()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#verifyWithRecaptcha%28java.lang.String%29) 메소드를 호출해야합니다. 
대개, 해당 메소드는 Activity View 상의 Button과 같은 컴포넌트에 바인딩되어 실행시킵니다.
verifyWithRecaptcha() 메소드 호출을 사용할 때 다음을 따라주세요.


1. API site key를 파라미터로 전달합니다.
2. [onSuccess()](https://developers.google.com/android/reference/com/google/android/gms/tasks/OnSuccessListener.html#onSuccess%28TResult%29) 와 [onFailure()](https://developers.google.com/android/reference/com/google/android/gms/tasks/OnFailureListener.html#onFailure%28java.lang.Exception%29)를 오버라이딩하여, 결과를 처리합니다.
    - onFailure() 메소드에서 [ApiException](https://developers.google.com/android/reference/com/google/android/gms/common/api/ApiException)를 리턴하였다면, [getStatusCode()](https://developers.google.com/android/reference/com/google/android/gms/common/api/ApiException#getStatusCode%28%29) 메소드를 통해서 상세 코드를 확인하여야합니다.
    - 자세한 사항은 아래의 **3) 에러 코드 및 설명** 부분을 참고해주세요(혹은 공식문서: [Handling communication errors](https://developer.android.com/training/safetynet/recaptcha#errors) 참조)


다음 예시처럼 사용합니다.

```kotlin
fun onClick(view: View) {
    SafetyNet.getClient(this).verifyWithRecaptcha(YOUR_API_SITE_KEY)
            .addOnSuccessListener(this as Executor, OnSuccessListener { response ->
                // Indicates communication with reCAPTCHA service was
                // successful.
                val userResponseToken = response.tokenResult
                if (response.tokenResult?.isNotEmpty() == true) {
                    // Validate the user response token using the
                    // reCAPTCHA siteverify API.
                }
            })
            .addOnFailureListener(this as Executor, OnFailureListener { e ->
                if (e is ApiException) {
                    // An error occurred when communicating with the
                    // reCAPTCHA service. Refer to the status code to
                    // handle the error appropriately.
                    Log.d(TAG, "Error: ${CommonStatusCodes.getStatusCodeString(e.statusCode)}")
                } else {
                    // A different, unknown type of error occurred.
                    Log.d(TAG, "Error: ${e.message}")
                }
            })
}
```


### 2) User Response Token 유효성 검사
reCAPTCHA API 가 [onSuccess()](https://developers.google.com/android/reference/com/google/android/gms/tasks/OnSuccessListener.html#onSuccess%28TResult%29) 메소드를 실행하였다면, 유저는 CAPTCHA 과정을 완수했다고 볼 수 있습니다. 다만, 이것은 유저가 CAPTCHA를 정확하게 풀었다는 것만을 의미하므로, 유저의 응답 토큰을 백엔드 서버로 보내어 유효성 검사를 수행할 필요가 있습니다.


### 3) 에러 코드 및 설명

| API Errors | Constant Value | Description |
| ------------- | ------------------- | -------------- |
| RECAPTCHA_INVALID_SITEKEY | 12007 | site key가 유효하지 않습니다. API key가 성공적으로 등록되었는지 확인하고, 정확한 site key가 입력되었는지 확인하세요. |
| RECAPTCHA_INVALID_KEYTYPE | 12008 | site key의 타입이 유효하지 않습니다. 새로운 site key를 생성해주세요. |
| RECAPTCHA_INVALID_PACKAGE_NAME | 12013 | 패키지명이 일치하지 않습니다.  |
| UNSUPPORTED_SDK_VERSION | 12006 | Android SDK 버전에서 지원하지 않습니다. Android SDK 를 업데이트하고 재시도하세요. |
| TIMEOUT | 15 | Timeout 입니다. 유저의 timeout 혹은 서버 response timeout입니다. 재시도해주세요. |
| NETWORK_ERROR | 14 | 인터넷 연결이 비정상입니다. 연결을 확인하세요. |
| ERROR | 13 | 일반적인 실패상황입니다. |

더 자세한 정보는 [SafetyNetStatusCodes](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetStatusCodes)를 참조하세요.