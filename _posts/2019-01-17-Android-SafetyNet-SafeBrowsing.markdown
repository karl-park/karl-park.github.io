---
layout: post
title:  "[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기"
date:   2019-01-17 09:00:00
tags: kotlin java android safetynet google security safebrowsing
categories: devstory
---

##### 다른 세션들
[[Android] SafetyNet Overview : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SafetyNet-Overview/) <br/>
[[Android] SafetyNet Attestation API : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SafetyNet-Attestation/) <br/>
[[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기](/devstory/2019/01/17/Android-SafetyNet-SafeBrowsing/) <br/>
[[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기](/devstory/2019/01/17/Android-SafetyNet-reCAPTCHA/)  <br/>
[[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기](/devstory/2019/01/17/Android-SafetyNet-VerifyApps/)


# SafetyNet Safe Browsing API
SafetyNet API를 통해서, 구글이 분류한 "위험요소가 있는" URL을 판별할 수 있습니다.

해당 파트에서는 SafetyNet이 어떻게 위험요소가 있는 URL을 체크하는지를 설명합니다.


## Prerequisite
##### Request and register Android API Key
1. [Google Developer Console](https://console.developers.google.com/project)에 들어갑니다.
2. 위의 툴바에서, **Project > 프로젝트_이름** 으로 들어갑니다.
3. *Safe Browsing APIs* 를 입력하여, 들어갑니다.
4. **Enable** 버튼을 클릭해 활성화하고, **Credential** 탭으로 들어갑니다.
5. **Add credentials to your project** 윈도우를 클릭합니다.
6. API Key 이름을 입력하고, **Create API key** 버튼을 눌러 API key 를 생성합니다.


<br/> 

##### API 초기화
다음 API를 사용하여 초기화를 진행합니다.
[initSafeBrowsing()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#initSafeBrowsing%28%29)

```kotlin
Tasks.await(SafetyNet.getClient(this).initSafeBrowsing())
```


## 1. URL 체크 요청하기
Google에서 현재 제공하고있는 threat types 는 다음 클래스를 이용해 살펴볼 수 있습니다.
[SafeBrowsingThreat](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafeBrowsingThreat)


URL check Request를 보내는 API 는 다음과 같습니다.
```kotlin
var url = "https://www.google.com"
SafetyNet.getClient(this).lookupUri(
        url,
        SAFE_BROWSING_API_KEY,
        SafeBrowsingThreat.TYPE_POTENTIALLY_HARMFUL_APPLICATION,
        SafeBrowsingThreat.TYPE_SOCIAL_ENGINEERING
    )
    .addOnSuccessListener(this) { sbResponse ->
        // Indicates communication with the service was successful.
        // Identify any detected threats.
        if (sbResponse.detectedThreats.isEmpty()) {
            // No threats found.
        } else {
            // Threats found!
        }
    }
    .addOnFailureListener(this) { e: Exception ->
        if (e is ApiException) {
            // An error with the Google Play Services API contains some
            // additional details.
            Log.d(TAG, "Error: ${CommonStatusCodes.getStatusCodeString(e.statusCode)}")

            // Note: If the status code, s.statusCode,
            // is SafetyNetstatusCode.SAFE_BROWSING_API_NOT_INITIALIZED,
            // you need to call initSafeBrowsing(). It means either you
            // haven't called initSafeBrowsing() before or that it needs
            // to be called again due to an internal error.
        } else {
            // A different, unknown type of error occurred.
            Log.d(TAG, "Error: ${e.message}")
        }
    }
```


## 2. URL Check 응답 확인하기
위 API 호출에서 [SafetyNetApi.SafeBrowsingResponse](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetApi.SafeBrowsingResponse)의 [getDetectedThreats()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetApi.SafeBrowsingResponse.html#getDetectedThreats%28%29)를 이용하여 Response를 체크할 수 있습니다.

만약, [SafeBrowsingThreat](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafeBrowsingThreat) 리스트가 보인다면, 위험 요소가 있는 것이고, 비어있는 리스트를 반환하였다면 API가 위험요소를 탐지해내지 못했음을 의미힙니다.
만약, 리스트가 비어있지 않다면, [getThreatType()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafeBrowsingThreat.html#getThreatType%28%29) 메소드를 이용해서 위험 타입을 얻어서 대처할 수 있습니다.


## 3. Safe Browsing Session 닫기
Safe Browsing API 를 한동안(더 이상) 쓰지 않을 때에는, [shutdownSafeBrowsing()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#shutdownSafeBrowsing%28%29) 메소드를 사용하여 사용을 중지할 수 있습니다.

```kotlin
SafetyNet.getClient(this).shutdownSafeBrowsing()
```


### 권장 사항들
- [shutdownSafeBrowsing()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#shutdownSafeBrowsing%28%29) : Activity의 onPause() 메소드에서 해당 API를 호출하는 것을 권장합니다.
- [initSafeBrowsing()()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#initSafeBrowsing%28%29) : Activity의 onResume() 메소드에서 실행하는 것을 권장합니다. 중요한점은, [lookupUri()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#lookupUri%28java.lang.String,%20java.lang.String,%20int...%29) API 호출 전에 반드시 initSafeBrowsing() 메소드가 호출되어야한다는 것입니다.