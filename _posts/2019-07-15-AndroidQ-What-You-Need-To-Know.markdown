---
layout: post
title:  "[Android] Android Q: What you need to know"
date:   2019-07-15 12:00:00
tags: android androidq version androido api29
categories: devstory
---

# Android Q
안녕하세요. 이번 시간에는 성큼 다가온 Android Q의 주요 대응점들을 살펴보고, Android 개발자로서 어떤 점들을 주의깊게 살펴봐야하는지를 다뤄보겠습니다.

## Android Q Preview
![android-q-what-you-need-to-know-1.png](/static/assets/img/posts/androidq/android-q-what-you-need-to-know-1.png)

현재 19년 7월 2일 기준, Beta 4(6월 5일)가 릴리즈되어있습니다.
[Android Q Beta4, API 29](https://developer.android.com/preview/release-notes#android_q_beta_4)


Android Q에서 대응해야할 점들을 살펴보기 전에, Android Q에서 변화되는 큰 줄기, 철학을 살펴보고 넘어가도록 하겠습니다. 이 방향을 알고있으면,  앞으로 변경되는 피쳐들을 이해하기 수월하기 때문입니다.

# Android Q의 세가지 철학
1. Security and Privacy 강화
    - Settings 하위에 있는 **Privacy** 섹션을 통해, 유저가 개인 정보와 관련된 모든 중요한 컨트롤을 할 수 있습니다.
    - Settings 하위에 있는 **Location** 섹션을 통해, 앱에 공유되는 위치 데이터를 확인할 수 있는 투명성을 제공합니다.
2. Innovation 강화
    - Foldable Phone, 5G 등 미래 기술에 대한 지원을 강화합니다.
    - Live Caption, Smart Reply 등과 같은 On-device machine learning을 지원합니다.
3. Digital Wellbeing 강화
    - 유저들의 휴대폰 사용량, 특정 앱 사용 등을 컨트롤 할 수 있도록 합니다.
    - Focus mode를 통해, 그들이 집중할 앱을 선택할 수 있게 합니다.

<br/>
"개발자" 관점에서는 아래의 특징들을 주의깊게 살펴봐야합니다.

1. **Transparency**
    - 유저 본인의 개인정보가 어떻게 사용되고 있는지 투명하게 확인이 가능해야합니다.
2. **User Control**
    - 유저 본인의 개인정보 제공 여부를 자유롭게 컨트롤 가능해야합니다.

---

# 1. Permissions, 권한
Android Q를 타겟으로 하는 앱들에서는 **`권한`** 에 더욱 신경써서 대응해야합니다.

권한은 Android 버전이 올라갈 때 마다 점점 더 폐쇄적으로 변하고 있으므로 기존에 오남용하는 권한들을 다시한번 체크해볼 필요가 있습니다.

특히 다음 권한에 신경써야합니다 : **`외부 저장소`**, **`위치(gps, network 등을 이용한 위치)`**, **`Wifi`**

## 1. Shared Storage (외부 저장소, External Storage)
```
- 대상 : Android Q Target 앱
- 영향 : 외부 저장소에 접근하거나 파일을 공유하는 기능 사용시 영향
- 대응 : App Sandbox 공간에서 앱 고유 파일 핸들링 및 공유되어야하는 파일들은 Media Collection Directory에 관리
```

Android에서 `외부 저장소`는 `World-Readable`로써, **READ/WRITE_EXTERNAL_STORAGE** 권한만 있으면, 다른 앱이 저장한 파일도 사용할 수 있었습니다.
이러한, 저장소 운영 방식은 특정 파일을 어떤 앱이 사용하고 있는지 모른다는 것과 파일을 사용한 앱이 삭제되었음에도 남아있는 이슈 등의 문제점들이 있었습니다.

이러한 점들로 인해 Android Q 이후에는 다음과 같은 변경 사항을 적용합니다.
1. 특정 파일을 사용중인 앱이 삭제되면, 파일도 같이 삭제되도록 변경
    - 앱 삭제 이후에 유지가 되어야하는 파일은 따로 처리해야함
2. 다른 앱의 파일을 볼 수 없도록 하여, 앱에 관련된 파일을 보호(sandbox)
3. 사용자가 어떤 파일에 언제 접근하는지를 인지 할 수 있도록 함

위와 같은 사항은 **`MediaStore`** 에는 해당되지 않습니다. **`MediaStore`** 은 추가 권한 없이 사용가능하며, 다른 앱이 저장한 콘텐츠를 보기 위해서는 **READ_EXTERNAL_STORAGE** 권한이 필요합니다.

그 외의 파일들은 **System File Picker**(시스템 기본 파일 선택기)를 통해 접근합니다.

| Android Q 이전 (API 28 이하)| Android Q 이후 (API 29 이상) |
| - | - |
|![android-q-what-you-need-to-know-2.png](/static/assets/img/posts/androidq/android-q-what-you-need-to-know-2.png) | ![android-q-what-you-need-to-know-3.png](/static/assets/img/posts/androidq/android-q-what-you-need-to-know-3.png) |
| - 내부 저장소 : 개별 앱만 접근 가능<br/> - 외부 저장소 : 권한만 있으면 누구나 접근 가능 | - Scoped Mode - 포토/비디오/오디오 외 텍스트 등의 기타 파일은 모두 MediaStore.Downloads에 별도 저장(시스템파일 선택기를 통해서만 접근 가능)<br/> - 원활한 마이그레이션을 위해 적용여부 선택 가능하게 플래그(requestLegacyExternalStorage) 제공<br/> - Android R에서는 모든 앱에 적용 예정 (**`1년의 유예기간, targetSdkVersion 에 관계없이 적용될 예정`**) |

<br/><br/>

Shared Storage 관련하여 Scoped mode 라는 모드를 신경써야합니다.

### Scoped mode
**`Scoped mode`** 는 다음 변경사항을 의미합니다.

1. **/sdcard** 영역 직접 접근 불가
    - 직접 접근 시, *FileNotFoundException*이나 *EPERM error* 발생함
2. **개별 앱 공간**
    - Sandbox 형태 (isolated)
    - 추가 권한 없이 읽고 쓰기가 가능함
    - 파일 경로로 직접 접근하는 것은 가능함 (getExternalFilesDirs 등)
    - 다른 앱으로 "파일 절대 경로 전달"이 불가능함(접근 불가능). 대신 ContentUri 를 사용하여 전달하여야 함
3. **공용 저장 공간**
    - 외부 저장소의 Public Files 공간이 사라짐
    - **MediaStore**, **Storage Access Framework(SAF)** API를 사용하여서 접근해야함.
    - 사진, 음악, 동영상, 다운로드 공간으로 나누어서 관리됨.
    - 특히, 다운로드 공간은 **System File Picker**를 통해 사용자가 명시적으로 접근해야 함.

### MediaStore
1. 앱이 제거된 이후에도 파일이 유지되어야하는 것에 사용합니다.
2. 다른 앱에서 사용 가능합니다.
3. 사용자가 다른 앱에서 보지 않아도 되는 파일이나, 앱이 제거 된 이후, 유지될 필요가 없는 파일은 MediaStore를 사용하면 안됩니다.
    - ex) 채팅앱에서 주고 받은 많은 미디어 파일들


## 2. Location
```
- 대상 : Android Q에 설치된 모든 앱
- 영향 : 백그라운드에서 유저의 위치 정보를 요청하는 기능 사용시 영향
- 대응 : 백그라운드에서 정말 위치 정보 추적이 필요한지 다시 한번 체크 및 필요시 ACCESS_BACKGROUND_LOCATION 권한 설정
```


사용자의 프라이버시 강화와, 안드로이드 단말기의 성능 저하를 보완하기 위해 Location Permission 관련한 변경이 있습니다.
기존의 Location 권한은 **항상 허용** 및 **허용 안함** 만 존재하였습니다.
Android Q(API 29 이상) 부터는 **앱 사용 중 일때만**이라는 선택지가 한개 더 추가되었습니다.

> 사용자가 **"앱 사용 중 일때만"** 권한을 선택한 경우에 기존 앱이 정상동작하는지 테스트가 필요합니다.

```
ACCESS_FINE_LOCATION
ACCESS_COARSE_LOCATION
ACCESS_BACKGROUND_LOCATION // 새로 추가된 Permission 입니다. 기존 "항상 허용"에 해당하는 권한입니다.
```

Android Q 미만에서는 `ACCESS_COARSE_LOCATION / ACCESS_FINE_LOCATION` 권한만 선언되어있다면, ACCESS_BACKGROUND_LOCATION 권한을 자동으로 삽입하여줍니다.

## 3. Wi-Fi Network 
```
- 대상 : Android Q Target 앱
- 영향 : Wi-Fi나 Wi-Fi Aware, Bluetooth scanning 등 wireless scanning 기능을 사용하는 앱
- 대응 : **ACCESS_FINE_LOCATION** 권한 설정
```

Android Q를 타겟으로 하는 앱에서는 Wi-Fi를 사용하도록 하거나, 사용중지 할 수 없습니다. 즉, 다음 API가 더 이상 동작하지 않습니다.

```java
// @Deprecated
// Build.VERSION_CODE#Q 이상부터는 항상 false를 리턴합니다.
WifiManager.setWifiEnabled(true | false)
```

만약 앱이, Wi-Fi 네트워크에 연결되어야하는 경우에는 다음 API를 사용하여 연결합니다.

1. Wi-Fi 네트워크에 연결하기를 원할 때
    - 위치 정보 권한 없이 P2P 환경에서 Wi-Fi 연결을 할 수 있습니다.
    - [NetworkRequest](https://developer.android.com/reference/android/net/NetworkRequest?hl=ko).[WifiNetworkSpecifier](https://developer.android.com/reference/android/net/wifi/WifiNetworkSpecifier?hl=ko)
2. 네트워크 사용을 위해 Wi-Fi 네트워크를 추가/삭제하고 싶을 때
    - 위치 정보 권한이 필요없습니다.
    - [WifiNetworkSuggestion](https://developer.android.com/reference/android/net/wifi/WifiNetworkSuggestion?hl=ko)
    - [addNetworkSuggestions()](https://developer.android.com/reference/android/net/wifi/WifiManager.html?hl=ko#addNetworkSuggestions%28java.util.List%3Candroid.net.wifi.WifiNetworkSuggestion%3E%29)
    - [removeNetworkSuggestions()](https://developer.android.com/reference/android/net/wifi/WifiManager.html?hl=ko#removeNetworkSuggestions%28java.util.List%3Candroid.net.wifi.WifiNetworkSuggestion%3E%29)

<br/>
만약 앱에, [ACCESS_FINE_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_FINE_LOCATION) 권한이 없다면, 다음 API 들을 사용 할 수 없습니다. (주로 Wifi나 Bluetooth 스캐닝을 하는 메소드들은 해당 권한이 필수로 변경됩니다.)

### Telephony

1. TelephonyManager
    - getCellLocation()
    - getAllCellInfo()
    - requestNetworkScan()
    - requestCellInfoUpdate()
    - getAvailableNetworks()
    - getServiceStateForSubscriber
    - getServiceState()
2. TelephonyScanManager
    - requestNetworkScan()
3. TelephonyScanManager.NetworkScanCallback
    - onResults()
4. PhoneStateListener
    - onCellLocationChanged()
    - onCellInfoChanged()
    - onServiceStateChanged()

### Wi-Fi

1. WifiManager
    - startScan()
    - getScanResults()
    - getConnectionInfo()
    - getConfiguredNetworks()
2. WifiAwareManager
3. WifiP2pManager
4. WifiRttManager

### Bluetooth

1. BluetoothAdapter
    - startDiscovery()
    - startLeScan()
    - LeScanCallback()


# 2. Background Activity
```
- 대상 : Android Q에 설치된 모든 앱
- 영향 : 백그라운드에서 Activity(UI가 보여지는 앱화면)를 호출하는 앱
- 대응 : Notification을 사용한 startActivity()로의 전환
```

Android Q에서 동작하는 `모든 앱`에서는 Background에서 호출하는 startActivity()가 제한됩니다.
Android Q에서 동작하는 모든 앱에 적용되는 정책입니다. (주의: targetApiversion과 관계없습니다.)

즉, 사용자의 상호작용 없이 "Activity"는 실행될 수 없습니다. "Notification"으로 trigger된 startActivity()로 대응 가능합니다.

![android-q-what-you-need-to-know-4.png](/static/assets/img/posts/androidq/android-q-what-you-need-to-know-4.png)

위 제약사항은 *개발자 옵션 > 백그라운드 Activity 시작 허용* 으로 예외 처리 할 수 있습니다.
(예를 들어, 테스트 시 사용 할 수 있습니다.)

### startActivity() 를 할 수 있는 조건

1. App이 Foreground에 있거나, "보이는 위치"에 있습니다.
2. App이 Foreground task의 백스텍에 activity를 가지고 있습니다.
3. App이 최근에 실행된 activity를 가지고 있습니다.
4. App이 최근에 activity를 종료하였습니다.
5. App에 시스템에서 Bind한 서비스들이 있습니다.
    - 서비스 목록 : AccessibilityService, AutofillService, CallRedirectionService, HostApduService, InCallService, TileService, VoiceInteractionService, VrListenerService 
6. App에 다른 앱(1번 조건, 보여지는 앱)에서 Bind한 서비스가 있습니다.
7. App이 시스템으로부터 PendingIntent 알림을 받습니다.
8. App이 다른 앱(1번 조건)에서 전송된 PendingIntent 알림을 받습니다.
9. App이 UI를 실행할 것으로 예상되는 시스템 브로드캐스트를 수신합니다. 예를 들어, ACTION_NEW_OUTGOING_CALL 과 SECRET_CODE_ACTION 이 있습니다.
10. App이 CompanionDeviceManager API 를 통해 다른 하드웨어 기기와 연동되어 있습니다. 이 API는 유저가 연동된 기기를 통해 App이 실행될 수 있도록 합니다.
11. App이 Device owner mode에서 동작하는 Device policy controller 입니다.
12. App이 유저로부터 **SYSTEM_ALERT_WINDOW** 권한을 받았습니다.


# 3. 시스템 UI

## 1. Dark Theme
```
- 대상 : Android Q에 설치된 모든 앱
- 영향 : 텍스트 컬러가 하드 코딩 되어있거나, 기본 텍스트 컬러를 사용하면서 하드 코딩된 배경색을 사용하는 경우, 또는 하드 코딩된 drawable icon 색상을 사용하는 경우
- 대응 : 하드코딩된 컬러 대한 체크 및 시스템 UI 의 경우, 다크테마를 적용하였을 때 앱의 UI와 잘 어울리는지 확인
```

Android Q 에서는 Dark 테마를 공식적으로 지원합니다.
앱별로 출시전, "다크 테마"를 선적용해보고 UI 상 어색한 점은 없는 지 등을 테스트해봐야합니다.

다음과 같은 방법으로 다크테마를 적용 할 수 있습니다.

1. System Settings > Display -> Theme -> Dark Theme
2. Notification 목록의 빠른 설정 타일을 사용해서 테마를 전환할 수 있습니다.
3. Pixel 기기에서는 절전모드가 되면 다크 테마가 사용됩니다.(다른 기기에서는 지원하지 않을 수도 있습니다.)
4. 앱에서 다음과 같이 DayNight 테마를 설정합니다.
    ```xml
    // styles.xml
    // AppCompat.DayNight 테마입니다.
    <style name="AppTheme" parent="Theme.AppCompat.DayNight">
    
    // 머터리얼 DayNight 테마입니다.
    <style name="AppTheme" parent="Theme.MaterialComponents.DayNight">
    ```


## 2. Gesture Navigation

```
- 대상 : Android Q에 설치된 모든 앱
- 영향 : 좌/우의 Gesture Area를 앱 자체 네비게이션 등으로 사용하는 앱. Home에 해당하는 Gesture Area에 UI 컴포넌트를 배치하여 사용중인 앱.
- 대응 : 좌/우 Gesture Area의 경우에는 오버라이드 하여, 앱 정상 작동을 보장. Home에 해당하는 UI는 변경 검토
```

다음과 같이 Full Gensture Navigation을 사용 할 수 있게 되었습니다. 이에 따라, 시스템이 사용하는 Gesture Area를 앱이 기본적으로는 사용할 수 없습니다.
특히 하단 Home 에 해당하는 영역은 Override도 할 수 없으므로 세심한 테스트가 필요합니다.

![android-q-what-you-need-to-know-5.png](/static/assets/img/posts/androidq/android-q-what-you-need-to-know-5.png)

만약 앱에서 해당 영역에 중요한 조작 버튼을 두었다면, "좌/우" 영역의 경우에는 오버라이드하여 조작 버튼 처리를 하는 것이 필요하고, 홈 버튼 영역에는 중요한 조작 버튼을 두지 않는 등의 조치가 필요합니다.


# 마치며
이 외에도, Android Q에서는 많은 변경 byyy사항이 있었습니다. (항상 그래왔듯이)
[Bubbles](https://developer.android.com/preview/features/bubbles) 등과 같은 신규기능이나, [Foldable Devices에 따른 지원](https://developer.android.com/preview/features/foldables) 등 눈여겨볼만한 피쳐들이 여러개 있습니다.

이 외에도, Non-SDK restrictions 등 legacy 코드들이 가질 수 있는 수정사항등을 서비스에 즉각적으로 반영해야할 수도 있습니다.

가능하다면, 시간을 내어 아래 References에 해당하는 부분을 천천히 읽어보고, 우리 서비스에 해당하는 내용은 없는지를 면밀히 검토해봐야합니다.


# References
- [Google Android Developers Blog | Android Q Beta4 andr Final APIs!](https://android-developers.googleblog.com/2019/06/android-q-beta-4-and-final-apis.html)
- [Android Q 개인정보 보호 체크리스트](https://developer.android.com/preview/privacy/checklist)


