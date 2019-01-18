---
layout: post
title:  "[Android] SafetyNet Attestation API : 기기/앱 위변조 검사"
date:   2019-01-17 09:00:00
tags: kotlin java android safetynet google security attestation
categories: devstory
---

##### 다른 세션들
[[Android] SafetyNet Overview : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SafetyNet-Overview/) <br/>
[[Android] SafetyNet Attestation API : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SafetyNet-Attestation/) <br/>
[[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기](/devstory/2019/01/17/Android-SafetyNet-SafeBrowsing/) <br/>
[[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기](/devstory/2019/01/17/Android-SafetyNet-reCAPTCHA/)  <br/>
[[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기](/devstory/2019/01/17/Android-SafetyNet-VerifyApps/)


# SafetyNet Attestation API
SafetyNet Attestation API를 통해서, 앱이 설치되는 기기를 분석할 수 있습니다. 앱이 실행되는 안드로이드 환경의 보안 정보와 호환성 정보를 획득 할 수 있습니다.

SafetyNet은 앱이 설치되는 기기의 소프트웨어와 하드웨어 정보를 조사하여, 디바이스의 프로파일을 생성합니다. 그 후, Android 호환성 테스트를 통과한 같은 프로파일을 가진 기기 목록을 찾습니다. 또한, SafetyNet은 해당 프로파일 정보를 이용하여, APK 정보 및 무결성을 검사하며, 기기의 변조, 변경 여부를 판단하고, 호출하는 앱이 legal한지를 확인 할 수 있습니다.

SafetyNet Attestation API의 목표는 앱을 실행하는 기기의 "무결성"을 확인하는 것입니다. 

> SafetyNet Attestation API를 유일한 anti-abuse system으로 사용하지 말고 여러 방어 시스템 중 하나로 사용하세요.

SafetyNet Attestation API는 Client-side, Server-side 컴포넌트로 나뉘며 아래에서 개략적인 Architecture를 살펴보실 수 있습니다.

##### Architecture
![image.png](/static/assets/img/posts/safetynet/attestation-1.png)

1. App에서 SafetyNet Attestation API 를 호출합니다. (to Google Play Service)
2. API는 Google 서버 통신을 하여, signed response를 요청합니다.
3. Google 서버에서 Google Play Service로 응답을 내려줍니다.
4. Google Play Service에서 Signed Response를 App으로 내려줍니다.
5. App에서는 signed response를 신뢰할만한 서버로 반드시 포워딩해줘야합니다.
6. 서버에서는 해당 요청을 검증하고 검증 결과를 App으로 응답합니다.


## Prerequisite
### Additional terms of service
SafetyNet APIs를 사용하기 위해서는 아래 링크의 Google APIs Terms of Service에 동의하여야합니다. (위의 Architecture에서 보는 것과 같이, 앱 및 디바이스의 프로파일 정보를 수집하여 구글 백엔드 서버로 보내기 때문입니다.)
- [Google APIs Terms of Service](https://developers.google.com/terms/)


### API Key 얻기
SafetyNet Attestation API를 사용하기 위해서는 API 키를 전달해야합니다. 다음 단계를 통해서 API Key를 얻을 수 있습니다.

1. Google APIs Console의 [Library page](https://console.developers.google.com/apis/library)로 들어갑니다.
2. Android Device Verification API 를 찾아서, 들어갑니다. Android Device Verification API 대시보드 스크린이 나타나야합니다.
3. API 사용을 하도록 클릭합니다. (Enable 버튼)
4. Create credentials 버튼을 클릭하여 API Key를 생성합니다. 만약 해당 버튼이 보이지 않는다면, All API credentials 드랍다운 리스트를 클릭하여, 이미 활성화 되어있는 API Key를 선택합니다.
5. 왼쪽 슬라이드바의 Credentials를 클릭하여, API key를 복사합니다. SafetyNetClient class의 [attest()](https://developers.google.com/android/reference/com/google/android/gms/safetynet/SafetyNetClient.html#attest%28byte%5B%5D,%20java.lang.String%29) 메소드를 호출할 때 마다 해당 API key를 이용합니다.

> 프로젝트의 모든 API key에서 하루에 10000개의 요청을 할 수 있습니다. 그 이상의 요청을 해야할 때는 [이 양식을 작성하여 제출](https://support.google.com/googleplay/android-developer/contact/safetynetqr)하세요.




### Google Play services version 체크
SafetyNet Attestation API를 사용하기 전에, Google Play service version을 반드시 체크해야합니다. 잘못된 버전이 설치되어있다면, API 호출 응답이 멈출 수 있습니다. 만약 잘못된 버전이설치되어있는 것 같다면, 유저에게 [Google Play service App](https://play.google.com/store/apps/details?id=com.google.android.gms) 업데이트를 하도록 권고해야합니다.

다음 코드 예시를 통해서, Google Play Service 버전이 사용중인 Android SDK 버전과 호환되는지 확인하세요.
- API : [GoogleApiAvailability.isGooglePlayServicesAvailable()](https://developers.google.com/android/reference/com/google/android/gms/common/GoogleApiAvailability#isGooglePlayServicesAvailable%28android.content.Context%29)

```kotlin
if (GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)
        == ConnectionResult.SUCCESS) {
  // The SafetyNet Attestation API is available.
}
```


## 1. Compatibility 체크
SafetyNet Compatibility 체크를 통해 앱이 실행되는 디바이스가 안드로이드 호환성 테스트를 통과하는지를 체크하고, Unknown source에 의해 앱이 위변조 되었는지를 체크해줍니다. 또한, 기기의 하드웨어, 소프트웨어 정보를 수집하여, 디바이스 프로파일을 생성합니다.

이상적으로는, Compability 체크를 통해서, 앱 내의 모든 중요한 작업(로그인, 구매, 인앱 제품 구매 획득 등)들을 보호하기 위해 검사를 수행하는 것이 좋습니다. 하지만, Compatibility 체크는 지연이나, 네트워크, 베터리 사용량을 증가시키기도 하므로, 균형을 잘 잡아야합니다.
예를 들어, 로그인 때 체크를 하고, 매 30분 마다 체크를 할 수도 있습니다. 혹은 위변조자가 체크 타이밍을 알 수 없도록, 서버에서 체크 타이밍을 관리할 수도 있습니다.

Compatibility 체크 API 사용을 위해서는 다음 지침을 따르세요.
1. Single use token을 획득합니다.
2. Compatibility check request를 보냅니다.
3. Response를 받습니다.
4. Response의 유효성을 검사합니다.

더 자세한 정보는 [Android Compatibility](https://source.android.com/compatibility/index.html)와 [Compativility Testing Suite, CTS](https://source.android.com/compatibility/cts-intro.html)를 확인하세요.

> Compatibility check는 네트워크 자원을 사용하므로, 응답 시간이 디바이스의 네트워크 상태에 따라 오래 지연될 수도 있습니다. 이에, 메인 쓰레드 외부에서 해당 작업을 진행하시기 바랍니다. Separate Execution thread에 대한 정보는 아래를 참조하세요.
> [Sending Operations to Multiple Threads](https://developer.android.com/training/multiple-threads/index.html)



### 1) Single Use Token 획득하기
SafetyNet API의 Compability check를 하기위해서는 일회성 토큰이 필요합니다. SafetyNet 요청과 함께 전송되는 nouce는 최소 16 바이트길이여야 합니다. nonce는 매번 달라져야하며, 서버로 보내는 데이터를 seed로 nonce를 생성해야합니다.

> 서버로 보내는 최대한 많은 데이터를 seed로 하여, nonce를 생성하면, 공격자가 공격하기 더 까다로워집니다.


### 2) Compability check Request
Google Play Service에 연결하고, nonce를 생성하였다면, 호환성 검사를 할 수 있습니다.
다음과 같이 요청을 보낼 수 있습니다.

```kotlin
// The nonce should be at least 16 bytes in length.
// You must generate the value of API_KEY in the Google APIs dashboard.
SafetyNet.getClient(this).attest(nonce, API_KEY)
        .addOnSuccessListener(this) { response ->
            // Indicates communication with the service was successful.
            // Use response.jwsResult to get the result data.
        }
        .addOnFailureListener(this) { e: Exception ->
            // An error occurred while communicating with the service.
            if (e is ApiException) {
                // An error with the Google Play services API contains some
                // additional details.
                // You can retrieve the status code using the
                // e.statusCode property.
            } else {
                // A different, unknown type of error occurred.
                Log.d(TAG, "Error: ${e.message}")
            }

        }
```


### 3) Read the Response
위 API를 통해서 Compability check를 보내면, 다음과 같은 응답을 받을 수 있습니다.
response객체는 [SafetyNetApi.AttestationResponse](https://developer.android.com/reference/com/google/android/gms/safetynet/SafetyNetApi.AttestationResponse.html)로 내려오고, [getJwsResult()](https://developer.android.com/reference/com/google/android/gms/safetynet/SafetyNetApi.AttestationResponse.html#getJwsResult%28%29) 메소드로 데이터를 얻을 수 있습니다. 응답은 [JWS(JSON Web Signature)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-36) 형식입니다.


```json
{
  "nonce": "R2Rra24fVm5xa2Mg",
  "timestampMs": 9860437986543,
  "apkPackageName": "com.package.name.of.requesting.app",
  "apkCertificateDigestSha256": ["base64 encoded, SHA-256 hash of the certificate used to sign requesting app"],
  "apkDigestSha256": ["base64 encoded, SHA-256 hash of the APK installed on a user's device"],
  "ctsProfileMatch": true,
  "basicIntegrity": true
}
```

위에서 나열한 파라미터들을 살펴봅시다.
- ctsProfileMatch: boolean - Android Compatibility Testing을 통과하였는지를 나타냅니다. 만약, 앱이 실행되는 디바이스의 프로파일이 안드로이드 호환성 테스트를 통과한 디바이스의 프로파일과 일치하면 true를 리턴합니다.
- basicIntegrity: boolean - 앱이 실행되는 디바이스가 변조되지 않음을 나타냅니다. 하지만, Android Compatibility Testing을 충분히 통과하지는 않을 수 있습ㄴ티다. (ctsProfileMatch 체크보다는 basicIntegrity 체크가 더 약합니다. CTS를 꼭 통과해야만 하는 경우가 아니라면 해당 파라미터를 사용할 수 있습니다. 자세한 정보는 이 링크를 참조하세요: [Possible attestation results](https://developer.android.com/training/safetynet/attestation#possible-results)
- apkPackageName, apkCertificateDigestSha256, apkDigestSha256 - 요청을 보내는 앱에 대한 정보를 담고 있습니다. 만약 해당 파라미터값이 누락되어있다면, API가 apk 정보를 정상적으로 얻지 못했음을 의미합니다. (ctsProfileMatch 가 true일 때만, 해당 apk 정보들을 신뢰할 수 있습니다.)
    - apkCertificateDigestSha256 파라미터를 통해 앱의 integirity를 검사하시길 바랍니다. (apkDigestSha256 필드는 신뢰할 수 없습니다.) 이 값은 대개 [PackageInfo.signatures](https://developer.android.com/reference/android/content/pm/PackageInfo.html#signatures) 필드가 리턴하는 certificate(s)의 해시값과 일치합니다.

<br/>
<br/>
<br/>

## 부록

##### Attestation result
다음은 가능한 Attestation 결과와 이에 대한 해석입니다.

| ctsProfileMatch | basicIntegrity | 디바이스 상태 |
| -------------------- | ----------------- | ---------------- |
| true | true | Certified, CTS를 통과한 Genuine 기기 |
| false | true | Unlocked bootloader를 가진 Certified 기기 |
| false | true | Uncertified, Genuine 기기(ex) 제조사가 certification을 적용하지 않은 기기) |
| false | true | Custom ROM을 가진 기기 (not Rooted) |
| false | false | Emulator |
| false | false | No device |
| false | false | Rooting등으로 System integrity가 훼손된 징후 |
| false | false | API Hooking등과 같이 다른 공격을 받은 징후 |

<br/>

##### Error Cases
JWS 응답 객체는 다음과 같이 에러 상황으로 응답할 수도 있습니다.
- null : Request가 성공적으로 수행되지 못했을 경우
- error : 네트워크 에러나 공격자에 의한 에러
    - 만약 1분에 5번 이상의 요청을 하면, api fixed rate limit을 초과하여 에러를 응답할 수도 있습니다.

<br/>

##### Advice for passing future checks
응답에는 advice 값이 내려올 수도 있습니다. advice 파라미터는 ctsProfileMatch나 basicIntegrity 값이 false일 경우에 내려오며, 왜 Compatibility check가 그런 결과를 내려줬는지를 설명합니다. 예로 다음과 같습니다.

```json
{"advice": "LOCK_BOOTLOADER,RESTORE_TO_FACTORY_ROM"}
```

- LOCK_BOOTLOADER
    - 유저는 그들 기기의 bootloader에 lock을 걸어야합니다.
- RESTORE_TO_FACTORY_ROM
    - 유저는 그들 기기를 clean factory ROM으로 복구해야합니다.

<br/>

##### 4. Response의 유효성 검증
JWS 메시지의 출처를 확인하려면 다음을 따라야합니다.
1) JWS message에서 SSL certificate chain 추출
2) SSL certificate chain 검증을하고, SSL hostname matching을 이용하여 leaf certificate가 hostname, "attest.android.com"에서 발급되었는지 검증합니다.
3) Certificate를 사용하여 JWS message의 signature를 검증합니다.
4) 처음 Requet에 보내었던 nonces, timestamps, package names, APK certificate SHA-256 hashes 등과 JWS message의 데이터가 일치하는지 확인합니다.

<br/>

###### Google APIs를 이용한 검증
Github에 있는 SafetyNet 샘플 예제에 수록되어있는 cryptographic 솔루션을 사용하여 JWS message를 검증합니다.
> Google Verify API는 하루 10000건 요청으로 제한됩니다. 초기 개발단계에서만 verify() 메소드를 사용해야합니다.

Android Device Verification API를 사용하기 위해서는 다음의 단계를 따라야합니다.
1) JWS message 전체를 포함하는 JSON 메시지를 만들어야합니다.
```json
{ "signedAttestation": "{getJwsResult() 메소드 결과}" }
```
2) HTTP POST 요청을 이용하여 보냅니다.
    - URL : https://www.googleapis.com/androidcheck/v1/attestations/verify?key={your-api-key}
    - METHOD : POST
    - Content-Type: "application/json"
3) 응답결과를 확인합니다.
```json
{ "isValidSignature": true }
```