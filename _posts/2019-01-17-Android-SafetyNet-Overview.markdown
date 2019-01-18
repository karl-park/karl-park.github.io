---
layout: post
title:  "[Android] 내 앱과 설치될 기기의 안전/안정성을 확보하기 : SafetyNet API"
date:   2019-01-17 09:00:00
tags: kotlin java android safetynet google security
categories: devstory
---

안녕하세요. 이번 시간에는 Google에서 제공하고 있는 SafetyNet 에 대해서 알아보는 시간을 가져보겠습니다.
아래의 각각의 SafetyNet 기능들을 살펴보고, 사용할 기능들의 문서를 읽어보시길 바랍니다. 

(영문 가이드를 의역하여 적었으므로, 다소 이해하기 힘든 부분이 있으실 수 있습니다.)

SafetyNet은 다음의 역할들을 합니다. (자세한 구현, 기능은 각 기능별 링크를 참조해주세요.)


### 1. 기기의 변조(루팅 등) 여부, 앱의 변조 여부를 확인 할 수 있습니다.
- `SafeNet Attestation API`
- Device 하드웨어, 소프트웨어 정보들을 프로파일로 만들어서, 구글 백엔드 서버와 통신하여, "내 앱"이 실행 될 디바이스를 분석 할 수 있습니다. 이를 통해, 위의 정보들을 체크합니다.
- [[Android] SafetyNet Attestation API : 기기/앱 위변조 검사](/devstory/2019/01/17/Android-SsafetyNet-Attestation/)


### 2. 내 앱에서 통신을 할 URL 들에 대해 안전성 검사를 할 수 있습니다.
- `SafetyNet Safe Browsing API`
- 통신을 할 URL을 구글 서버로 보내어, 구글이 관리하고 있는 위험한 사이트인지 체크합니다.
- [[Android] SafetyNet Safe Browsing API : 위험한 URL인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-SafeBrowsing/)


### 3. reCAPTCHA를 통해서, Bot인지 사람인지를 판별 할 수 있습니다.
- `SafetyNet reCAPTCHA API`
- 스팸 등의 어뷰징으로부터 앱을 보호 할 수 있는 무료 서비스를 제공합니다.
- [[Android] SafetyNet reCAPTCHA API : reCAPTCHA 사용하여 봇인지 체크하기](/devstory/2019/01/17/Android-SsafetyNet-reCAPTCHA/) 


### 4. 설치되어있는 앱들 중, 실제로 (또는, 잠재적으로) 유해한 앱들이 있는지를 확인 할 수 있습니다.
- `SafetyNet Verify Apps API`
- 해당 기능 사용을 위해서는 사용자가 Google Play Service의 Verify Apps 기능을 활성화해야합니다. 해당 기능 사용 여부를 확인하고, 활성화를 요청하는 API가 제공됩니다.
- [[Android] SafetyNet Verify Apps API : 설치된 악성앱들 목록 받기](/devstory/2019/01/17/Android-SsafetyNet-VerifyApps/)



##### References
- [Android SafetyNet Document](https://developer.android.com/training/safetynet/)
- [Android SafetyNet Samples on Github(ClientApp(Android) and Server(Java & C#)](https://github.com/googlesamples/android-play-safetynet)