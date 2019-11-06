---
layout: post
title:  "[iOS] ATS - Checking Security"
date:   2016-09-06 03:00:00
tags: objc ios ATS security
categories: devstory
---
2017년을 준비하는 iOS 개발자들은 한결같이 `ATS` 이슈를 대응하고 있을 것이다. 그렇다면 ATS란 무엇이고, 어떤점을 대응해야하는지 살펴보자.


# ATS란
App Transport Security의 줄임말이다.
iOS에서는 NSURLSession(NSURLConnection)등을 이용하여 인터넷 연결을 수립하게 된다. 이 때, HTTP 주소로 접속을 하거나, HTTPS라 하더라도 Apple의 요구사항을 만족하지 못하는 도메인으로 접속시 ATS에러가 나게 된다.

iOS9 이상(MacOS 10.11 이상)에서 요구되며, 권장 요구사항을 만족못할 경우, 연결이 실패된다.

## ATS 예외
사실 모든 사이트가 HTTPS가 되면, 보안상 안전하겠지만, 소규모 업체 등에서는 인증서 발급/관리 등이 사실상 불가능하기에 접속 자체가 불가능한 이슈가 있다. 이를 위해 ATS를 예외 처리할 수 있다.

ATS에 대한 기능 관리는  Xcode Project의 `Info.plist`에서 할 수 있다.

### ATS 기능 Off
ATS 를 우회하기 위해 ATS의 전체 기능을 꺼버릴 수 있다.
물론 절대 권장하지 않는다. 앱 심사시 리젝을 당할 수 있으며,
탄탄한 근거로 소명을 해야 통과를 할 수 있다.

물론, `개발`시에는 ATS 기능을 아예 꺼놓고 동작 테스트를 하는 것이 여러모로 골치가 안아플 수 있으니, Production Level과 구분하여 사용하도록 하자.

ATS 기능을 완전히 끄기 위해서는 Info.plist의 `NSAppTransportSecurity > NSAllowsArbitaryLodas` 값을 `YES`로 설정해주면 된다.

```xml
...
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
...
```


### ATS WhiteList 설정
위에 이미 언급했다시피 ATS를 끄는 것 보다는 WhiteList를 입력함으로써, 보안도 챙기면서 리젝도 안당할 수 있는 방법이 있다.

다음과 같이 WhiteList를 Info.plist의 `NSAppTransportSecurity > NSExceptionDomains`에 입력한다.

```xml
...
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>karl-park.github.io</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <true/>
            ...
        </dict>
    </dict>
</dict>
...
```

위의 `NSExceptionDomains`를 입력하다보면, 여러가지 세부 설정값들이 있다.

그에 관한 사항은 다음 링크를 참조하자
[Apple Guide - ATS COnfiguration Basics](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)





# Checking Security : ATS
그렇다면, 어떠한 도메인이 ATS 요구사항을 충족하는지는 어떻게 체크할 수 있을까?
다음의 명령어를 이용하여 체크가 가능하다.


## ATS Check
```shell
$ /usr/bin/nscurl --ats-diagnostics --verbose https://apple.com
...
> PASS


$ /usr/bin/nscurl --ats-idagnostics --verbose https://webholic.kr
...
> FAIL
This command will tell you which settings of info.plist will success.

you should catch these concepts about http://, https://, and https:// (with PFS).
```


ATS 설정을 함에 있어서, Arbitary Loads 를 YES로 켜놓으면 그 얼마나 쉽겠냐만,
보안상 너무나도 취약하니 ATS 설정을 제대로 신경써주자 !
더군다나 내년(2017)부터는 TLSv1.2가 의무이니, 미리 준비해서 나쁠것은 절대 없다.

