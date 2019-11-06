---
layout: post
title:  "[Android] Wifi로 디버깅하기"
date:   2018-05-16 09:00:00
tags: java android debug wifi
categories: devstory
---
Android Device를 `디버깅`할 때, usb로 항상 물려서 테스트를 해야해서 여간 번거로웠습니다.

디바이스와 디버그할 기기가 물리적으로 "붙어있어야만"했기 때문이죠.

그래서 `wifi` 등으로 원격 디버깅을 할 수 없을까?란 고민을 하게되었고, 방법이 있음을 발견했습니다.



## Steps
1. 호스트 컴퓨터와 디바이스를 같은 Wifi 망에 연결합니다.
2. USB 케이블로 기기와 호스트를 연결합니다.
3. 터미널을 연 후, 다음 명령어를 입력하여, **해당 포트에서 tcp/ip** 연결을 대기하도록 설정합니다.
    ```shell
    $ adb tcpip {portNumber}
    
    // example
    $ adb tcpip 5555
    ```
4.  USB 연결을 끊습니다.
5.  디바이스의 Wifi 주소를 찾습니다.
    ```
    Galaxy 예) `설정 > 연결 > WIFI > 고급 > IP 주소`
    Nexux 예) `설정 > 시스템 > 휴대전화 정보 > 상태 > IP 주소`
    ```
6. 호스트 컴퓨터에서 다음 명령어를 쳐서 연결합니다.
    ```shell
    $ adb connect {wifi address}
    
    // example
    $ adb connect 10.77.102.33
    
    // 연결 수립 확인
    $ adb devices
    > List of devices attached
    > 10.77.102.33:5555           device
    ```
    

# Plugin 사용
Android Studio 플러그인을 사용하면 위와 같이 번거로움을 피할 수 있다고 합니다 !
[https://github.com/appdictive/ADBWiFiConnect](https://github.com/appdictive/ADBWiFiConnect)


## Reference
- Android Developer Guide : https://developer.android.com/studio/command-line/adb?hl=ko#wireless