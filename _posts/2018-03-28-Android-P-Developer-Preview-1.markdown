---
layout: post
title:  "Android P, Developer Preview1"
date:   2017-08-22 14:30:00
categories: [Android]
tags: [Android,Android P,Android 9,DP1,Preview]
---



# What's new for developers in Android P


# Android P Behavior Changes

## I. 모든 API에 적용되는 변경사항
### 1. Input and data privacy in background apps
>  백그라운드에 있는 앱이 유저의 input/sensor 데이터에 접근하는 것을 제한함으로써, privacy를 강화합니다.
- microphone, camera에 접근 할 수 없습니다.
- [continuous reporting mode](https://source.android.com/devices/sensors/report-modes#continuous)를 사용하는 센서들(가속도나 자일로스코프)은 이벤트를 받지 못합니다.
- [on-change](https://source.android.com/devices/sensors/report-modes#on-change)나 [one-shot reporting mode](https://source.android.com/devices/sensors/report-modes#one-shot)를 사용하는 센서들은 이벤트를 받지 못합니다.
- 만약, Android P에서 sensor 이벤트를 받아야한다면, [foreground service](https://developer.android.com/guide/components/services.html#Foreground)를 사용해야합니다.
- [SensorManager](https://developer.android.com/reference/android/hardware/SensorManager.html)의 [flush()](https://developer.android.com/reference/android/hardware/SensorManager.html#flush%28android.hardware.SensorEventListener%29) 메소드를 호출하는 앱에는 위 이 변경사항이 적용되지 않습니다.


### 2) Device security changes
> Android P에 해당하는 기기들은 key rotation과 system call protection을 지원합니다.
- https://developer.android.com/preview/features/security-behav.html#all-apps
- ``````위를 참조`````



### 3) Cryptographic changes
> 암호화 알고리즘 구현에 대해 Android P에서는 몇몇 수정이 있었습니다.

- [Conscrypt](https://github.com/google/conscrypt)의 파라미터로 AES, DESEDE, OAEP 등의 알고리즘 구현체를 제공합니다. [Bouncy Castle](https://www.bouncycastle.org/) 버전의 알고리즘 등 많은 알고리즘이 Android P 에서 eprecated 되었습니다.
    - Android 8.1(API 27) 이하 : deprecated된 Bouncy Castle 구현체를 호출하였을 경우, "warning" 메시지를 받습니다.
    -  Android P : deprecated된 Bouncy Castle 구현체를 호출하였을 경우, [NoSuchAlgorithmException](https://developer.android.com/reference/java/security/NoSuchAlgorithmException.html)이 발생합니다.
    - 기타 다른 변경사항은 [링크](https://developer.android.com/preview/behavior-changes.html#other_changes)를 참조합니다.


### 4) App compatibility changes
> 앱의 안정성과 호환성을 제공하기 위해서, reflection/JNI등을 통한 Non-SDK 메소드/필드 호출을 제한합니다.
- non-sdk 란 쉽게 말해서, Android 공식 문서에 포함되지 않은 메소드/필드를 의미합니다. 언제든지 변경될 수 있으므로, 가급적 사용을 하지 말라는 것이 권고안이었습니다. 이번 Android P DP1(Developer Preview1)을 적용하면 logcat과 Toast 등으로 non-sdk 사용 여부를 알려줍니다. 이 때, greylist 로 알려주는데 dark greylist는 다음 Developer Preview 배포시 접근이 제한 될 수 있음을 알려줍니다.(일반 greylist는 잠재적 위험이 있는 request입니다.)



### 5) Updates to the ICU libraries
> ICU Library가 58(API 26, 27에서 지원)에서 60으로 업데이트가 됩니다.
- ICU은 International Components for Unicode의 약자로서, **android.icu package**에서 지원합니다.
- 더 이상, UTC와 GMT를 동일하게 처리하지 않습니다.
- 자세한 사항은 [링크](https://developer.android.com/preview/behavior-changes.html#icu)에서 참고하세요.



### 6) Android secure encrypted files are no longer supported
> ASECs(Android Secure Encypted Files) 기능이 삭제되었습니다.
- ASECs는 Android 2.2(API 8)에서 처음 소개되되어, apps-on-SD-card 기능을 지원하기 위해 추가되었습니다.
- Android 6.0(API 23)은 "Adaptable SD Card"기능을 이용해 기존 ASECs를 대체하였으며, ASECs 기능을 deprecated 시켰습니다.
- Android 8.0(API 26)은 ASECs로 새로운 앱을 설치하는 것을 막았으며, 이번 DP1에서는 ASECs 기능을 완전히 삭제하였습니다.


### 7) Test suite build changes
> TestSuiteBuilder 클래스가 depreacted 되면서, addRequirements() 메소드가 삭제되었습니다.


### 8) Testing libraries removed from framework
> Android P에서는 프레임워크에서 test class를 없앴습니다. 대신에, android.test.base, android.test.runner, android.test.mock의 Junit-based 클래스 라이브러리로 재구성하여 제공합니다.
- Android 8.1(API 27) 이하에서는 ActivityInstrumentationTestCase2와 같은 테스트 클래스들을 이용하였습니다. 이러한 테스트는 편리한 반면, android.jar JUnit에 종속성이 생겼으므로, 다른 버전의 JUnit에 의존하게 되는 테스트 dependency와 빌드를 어렵게 만듭니다.
- 제거된 테스트 클래스들은 optinal dependencies로 계속 사용가능합니다.


### 9) Java UTF decoder
> UTF-8 포멧은 Android의 default 문자셋입니다. Android P 에서는 UTF-8 포멧에 대하여 보다 엄격한 기준을 적용합니다.
- 자세한 사항은 [링크](https://developer.android.com/preview/behavior-changes.html#decoder)를 참조하세요.


### 10) Hostname verification using a certificate
> CN(commonName)이 RFC 2818에서 deprecated됨에 따라서, CN을 이용한 hostname fall back을 하지 않습니다.
- RFC 2818에서는 Domain Name을 찾기 위해 먼저 SAN(subjectAltName)으로 찾고 SAN이 없을 시,  CN(commonName)을 사용해 찾는 방법이 정의 되어있습니다. 하지만 RFC 2818에서 CN이 deprecated됨에 따라, Android에서는 더 이상 CN을 이용한 fall back을 하지 않습니다.
- 이에, hostname 검증을 위해서는 서버에서 항상 알맞은 SAN을 포함한 잉증서가 존재해야합니다.



### 11) Network address lookups can cause network violations
> Network 주소 lookup 작업은 네트워크 I/O를 발생시키며, 메인스레드에서 작업시, 블록킹을 유발할 수 있습니다.
- [StrictMode](https://developer.android.com/reference/android/os/StrictMode.html)를 이용하면 이러한 network violation 상황을 발견할 수 있습니다.
- StrictMode 클래스 사용은 배포버전에는 나가면 안되므로, 주의합시다.


### 12) Socket tagging
> Android P 부터는 태깅된 소켓이 binder IPC 호출로 다른 프로세스로 넘어갔을 때애도 태깅이 유지됩니다.
- Android P 이전에서는 소켓 태깅이 되었을 때([setThreadStaatsTag() 호출](https://developer.android.com/reference/android/net/TrafficStats.html#setThreadStatsTag%28int%29)), [binder IPC](https://source.android.com/devices/architecture/hidl/binder-ipc#ipc)를 이용해 다른 프로세스로 보내졌을 때 untagging 되는 문제점이 있었습니다. 


### 13) Reported amount of available bytes in socket
> [shoutdownInput()](https://developer.android.com/reference/java/net/SocketImpl.html#shutdownInput%28%29) 메소드 호출 이후에 [SocketImpl.available()](https://developer.android.com/reference/java/net/SocketImpl.html#available%28%29) 메소드 호출시 0을 리턴합니다.



### 14) More detailed network capabilities reporting for VPNs
> 더 상세한 VPN 정보를 제공합니다.
- Android P 이전에는 [NetworkCapabilities](https://developer.android.com/reference/android/net/NetworkCapabilities.html) 클래스가 NET_CAPABILITY_NOT_VPN 등은 생략e된 제한된 VPN 정보를 주었습니다.


### 15) Files in xt_qtaguid folder are no longer available to apps
> /proc/net/xt_qtaquid 폴더 내의 파일들에 대한 직접적인 읽기가 금지됩니다.
- 위의 폴더 내 파일들이 없는 Android P 기기들에 대한 일관성을 보장하기 위해서입니다.


### 16) FLAG_ACTIVITY_NEW_TASK requirement is now enforced
> [FLAG_ACTIVITY_NEW_TASK](https://developer.android.com/reference/android/content/Intent.html#FLAG_ACTIVITY_NEW_TASK) 인텐트 선언없이는 non-context에서 Activity를 시작할 수 없습니다.
- 만약 해당 인텐트 설정없이 startActivity등을 한다면, Activity노출 없이 로그 메시지로 실행결과를 알려줍니다.
- 원래에도 요구된 사항이었으나 Android N에서 버그로인해 적용되지 않은 동작이었습니다.


### 17) Screen rotation changes
> Android P 부터는 **portrait** 모드의 이름이 **rotation-lock** 모드로 이름이 바뀌었습니다.
- Android O에서는 QuickSettings tile/DisplaySettings를 사용하여 **auto-rotate** 와 **portrait** 모드 중 한개를 골라서 orientation을 설정했습니다. Android P에서는 portrait이름이 변경되었습니다.
- 기타 회전 변경 사항에 대해선 [링크](https://developer.android.com/preview/behavior-changes.html#screen-rotation-changes)를 참조하세요.




## II. Android P Target에만 적용되는 변경사항
### 1) Foreground Services
> Android P 이상의 기기에서 foreground service를 사용하려면 [FOREGROUND_SERVICE](https://developer.android.com/reference/android/Manifest.permission.html#FOREGROUND_SERVICE) 권한을 신청해야합니다. 그렇지않으면, [SecurityException](https://developer.android.com/reference/java/lang/SecurityException.html)이 발생합니다.


### 2) Device serial access restrictions
> Android 8.0(API 26)에서 [Build.SERIAL](https://developer.android.com/reference/android/os/Build.html#SERIAL)이 deprecated 되었고, Android P에서는 "UNKNOWN"으로 반환됩니다.
- 만약 Device hardware serial number를 사용하려면,[READ_PHONE_STATE](https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_STATE) 권한 신청 후, [Build.getSerial()](https://developer.android.com/reference/android/os/Build.html#getSerial%28%29) 메소드를 사용하여 얻습니다.


### 3) Framework security changes
> Android P에서부터는 네트워크와 파일 security에 있어서 보다 엄격하게 시스템에서 관리합니다.
- 자세한 사항은 [링크](https://developer.android.com/preview/features/security-behav.html#p-apps)를 참조하세요.


### 4) View focus
> 폭이나 높이가 0인 View는 더 이상, focusing 되지 않습니다. 
> Android P 이상에서부터, 웹뷰에서 [CSS Color Module Level 4](https://drafts.csswg.org/css-color/#hex-color)를 지원합니다.
- 기존에는 #0000ffcc(rgb(0,0,255), alpha:80%) 라는 CSS color Module Level 4 값이, #00ffcc 로 웹뷰에서 렌더링되었습니다. 이제는 #0000ffcc 그대로 지원가능합니다.


### 5) Document scrolling element
> Android P 이전에는 WebView에서 scrolling 시작 기준이 document.body 였습니다. Android P 부터는 document.root ! 부터 스크롤 값이 계산됩니다.
- 이로 인해서, "document.body.scrollTop, document.body.scrollLeft, document.documentElement.scrollTop or document.documentElement.scrollLeft" 값들이 target SDK에 따라서 다르게 계산됩니다. "document.scrollingElement"로 대체하여 사용해야합니다.




## Reference
- android : https://developer.android.com/preview/index.html