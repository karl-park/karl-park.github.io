---
layout: post
title:  "[MWDC2017] 2nd Day"
date:   2017-04-08 03:00:00
tags: seminar mwdc2017 MWDC MobileWebDevCon Conference
categories: devstory
---
지난 시간에 이어서, MWDC의 2번째 날에 들었던 내용을 정리하는 시간을 가져보겠습니다.

- [MWDC Day1](../MWDC2017-Day-First/)
- [MWDC Day2](../MWDC2017-Day-Second/)


## TOC

- 0 MWDC란?
- 1 Lazy loading JS modules in the Browser
- 2 iOS Testing & Debugging
- `3 KeyNote: Optimizing ur app for profitability`
- `4 Delivering a better UX with in-app support`
- `5 Be offensive: Proactively Assessing Your iOS`
- `6 Scaratch that: Building an app in Swift`
- `7 iOS Tools Overview`
- `8 Building Successful apps for Africa`


---
# 3. KeyNote: Optimizing ur app for profitability


## 앱 최적화를 위한 5가지 !!
![Inline-image-2017-04-10 14.29.29.181.png](/static/assets/img/posts/mwdc/mwdc-2nd-001.png)

### 1. 고객 데이터
- New VS Returning 유저 분석
    - 히스토리
    - Acquisition Source
    - CLV(Customer Lifetime Value)/LTV(Life-Time Value)

### 2. 디자인
- A/B Testing
- Usability Testing


### 3. 성능 모니터링
- Page Load Times
- Startup Time
- Crash Rate
- Device & OS Support
- Web Service & API Calls

### 4. ASO / SEO
- ASO : for Mobile Apps
    - is used to increase rank over app stores
- SEO : for Web Apps
    - is manipulating search algorithm to get better ranks for websites
- 즉 검색 최적화 및 상위 랭크 노출


### 5. 분석
- Micro KPIs (Key Performance Indicators)
    - 핵심성과지표 (마케팅에서 엄청 중요한 개념)
    - ex) Gamebase 로그인 시간
        - 0~0.3 우수
        - 0.3~0.5 : 양호
        - 0.5~1.0 : 중간
        - 1.0 ~ : 불량
- Active Users/Time





---
# 4. Delivering a better UX with in-app support
- In-app support 기능에 대한 세션

## In App Support에는 어떤것들이 있을까?
- Contact us
    - 사용자의 문의/컨텍은 반응속도가 중요하다. 유저가 편리하게 입력할 수 있도록 하는 UX 제공 !
    - ![Inline-image-2017-04-10 21.09.15.141.png](/static/assets/img/posts/mwdc/mwdc-2nd-002.png)
- FAQ
    - ![Inline-image-2017-04-10 21.11.39.246.png](/static/assets/img/posts/mwdc/mwdc-2nd-003.png)
- Boost App Revies
    - 적절한 시간에, 적절한 문구로, 사용자에게 노출되어야함
    ![Inline-image-2017-04-10 21.08.36.330.png](/static/assets/img/posts/mwdc/mwdc-2nd-004.png)

## In App Support 가 가져다주는 것들

![Inline-image-2017-04-10 21.08.11.328.png](/static/assets/img/posts/mwdc/mwdc-2nd-005.png)




---
# 5. Be offensive: Proactively Assessing Your iOS Applications.
- 보안 보안 보안 !! 대책을 강구하시오 !!

## What does being offensive mean ?
- Be Proactive == Be Offensive
- Too soon? better than talking about it later
    - OWASP : The Open Web Application Security Project
    - https://www.owasp.org/index.php/Mobile_Top_10_2016-Top_10

## OWASP
1. Weak Server Side Controls
2. Insecure Data Storage
    - Local 에 저장을 하면 안되곘죠? (중요 민감한 데이터)
    - SQLite DB
    - Log Files
    - Plist Files
    - XML Data Stores or Manifest Files
    - Binary Data stores
    - Cookie Store
    - SD Card
    - Cloud Synced
3. Insufficient Transport Layer
    - Apple TLS1.2 --> 미국에서도 애플보고 shut it up 이라고 하는구나 !
    - App Transport Security iOS 9/10 -> NSAllowsArbitaryLoads true !!!!! Please No !
4. Untenteded Data Leakage
    - Platform cache storage
    - Clipboard data
        - nil로 처리를 해줘서, 로그아웃등에도 비밀번호 등이 남아있는 것을 방지 !!
    - Debug ...
5. Poor Authorization and Authentication
    - Psychological Acceptability
    - Spoofable values used for authentication
        - Deice IDs / Geo-locations 등은 쓰지말자...
    - Client-side A&A
    - Fingerprint Readers
6. Broken Cryptography
    - Nothing differnt than what we have heard before
        - CRYPTO IS REALLY HARD !
    - salt 같은 경우는 코드에 하드코딩하지말고, 서버에서 받거나.. 하는 식으로 하자 !
7. Client Side Injection
    - SQLite Injection
    - JavaScript Injection(XSS)
    - mail title로 아래와 같이하면 접근이 가능(했었다)
    ```html
    <iframe src='file:///proc/self/...' .../>
    ```    
8. Security Decisions via Untrusted Inputs
    - Inter Process Communication(IPC)
        - openURL 에서 authorization 을 확인하자 !!!!!
        ```html
        <iframe src="skype://14124124124?call"></iframe>
        ```        
9. Improper Session Handling
    - Logout을 눌렀을 때, 세션을 핸들링하도록하자 !
    = Failure to validate sessions on backend
    - Inadequate or improperly managed Session Timeouts
        - Client AND server side
    - Cookie problems
10. Lack of Binary Protections
    - IMHO (Security by obscurity)
    - Disabling Code Encryption
    - Jailbreak Detection Evasion
    - Class Dumping
    - Method Swizzling
    - Runtime Code Injection, Motitoring and Analysis
    - Reverse Engineering
    - Bytecode Conversion
    - Disassembly


## Be Offensive
1. Write and Test Security Stories !!
    - Wagile : Complete All the stories !!
2. Test APIs Too !!
    - Web Sockets / HTTP(S)/COAP/UDP/TCP <--> The Internetz <--> APIs
3. Incorporate Testing Tools DURING Development
    - Common Tools
        - IDB
            - Lots of FUN in one box
        - Proxy Tools
            - Burp, ZAP, etc
            - rvictl
        - Cycript
        - MobSF
4. Use REAL Devices !
    - The Simulator is a garbage !
5. Learn the Device and Jailbreak them
    - Jailbreak
        -Yalu - 10.0.0-10.2
        - Pangu - 9.0-9.1 and 9.2-9.3.3
    - File Viewers
    - iExplorer
    - iFUnBox
    - Keychain
6. Test Prod Deployments
    - Development <------> Test Boundary <------> App Store
    - What to examine?
        - Who is IPA signed as?
            -codesign -dv -verbose = 4 APP_BINARY
        - TLS?
            - sslabs



---
# 6. Scaratch that: Building an app in Swift
원래 회사원이 전부 ObjC 개발자

"야 스위프트로 한번 해보자. 우리 빼고 전부 스위프트 하더라"

"그래 !! 우리 앱을 싹 갈아엎어보자 !!"

...

"확실히 Swift가 좋긴 좋구나 !!"


## git
- git : https://github.com/d2burke/matchup


---
# 7. iOS Tools Overview

## IDE
- ![Inline-image-2017-04-17 20.55.57.942.png](/static/assets/img/posts/mwdc/mwdc-2nd-006.png)
    - Xcode !
- ![Inline-image-2017-04-17 20.54.57.933.png](/static/assets/img/posts/mwdc/mwdc-2nd-007.png)
    - AppCode ! (jetbrains)


## Lint
- SwiftLint
- OCLint
    - http://oclint.org/

```shell
$ brew tap oclint/formulae
$ brew install oclint

$ brew update
$ brew upgrade oclint

(http://docs.oclint.org/en/stable/intro/homebrew.html)


$ gem install xcpretty

> ~~~ Build Phase
source ~/.bash_profile
cd ${SRCROOT}
xcodebuild clean
xcodebuild | xcpretty -r json-compilation-database
oclint-json-compilation-database -- -report-type xcode
~~~

(http://docs.oclint.org/en/stable/guide/xcode.html)

$ xcodebuild -project Gamebase.xcodeproj/ |xcpretty -r json-compilation-database --output compile_commands.json

RUN !
```


## Template
Template Links: [https://developer.apple.com/ios/human-interface-guidelines/resources/](https://developer.apple.com/ios/human-interface-guidelines/resources/)

## Source Control
1. GitHub
2. GitLab
3. Cocoapods for dependencies
4. Carthago (~=CoCoapods)
5. Git-Tower - desktop app for repo management


## Testing + Automation
- XCTest
    - https://developer.apple.com/library/content/documentation/DeveloperTools/
Conceptual/testing_with_xcode/chapters/03-testing_basics.html#
- Fastlane
    - https://fastlane.tools
- Jenkins
    - https://jenkins.io
- Bots
    - https://developer.apple.com/library/prerelease/content/documentation/IDEs/
Conceptual/xcode_guide-continuous_integration/ConfigureBots.html#



---
# 8. Building Successful apps for Africa
## 4-data saving strategies for Android

### 1. Image reduction
- Image Format
    - 1. WebP
        - ![Inline-image-2017-04-17 21.53.26.316.png](/static/assets/img/posts/mwdc/mwdc-2nd-008.png)
        - WebP > PNG
    - 2. CloudFlare Polish
        - ![Inline-image-2017-04-17 21.54.26.668.png](/static/assets/img/posts/mwdc/mwdc-2nd-009.png)
- Image size
    - ![Inline-image-2017-04-17 21.55.14.778.png](/static/assets/img/posts/mwdc/mwdc-2nd-010.png)
- User defined data profile
    - ![Inline-image-2017-04-17 21.55.29.228.png](/static/assets/img/posts/mwdc/mwdc-2nd-011.png)


### 2. API response structure
- 데이터가 얼마나 자주 변하는지 !
    - 거의 변화가 없는 API Response data는 Caching 정책을 세우자 !
    - Rerely changes vs. Changes often

### 3. Push Messages
- Push !!
    - ![Inline-image-2017-04-17 21.57.12.197.png](/static/assets/img/posts/mwdc/mwdc-2nd-012.png)
    - ![Inline-image-2017-04-17 21.57.41.412.png](/static/assets/img/posts/mwdc/mwdc-2nd-013.png)


### 4. APK size
- Images
    - Use Vector Graphics : resolution-independent !
- ProGuard
    - 미사용 코드/리소스 제거
    - https://developer.android.com/studio/build/shrink-code.html?hl=ko





- ![Inline-image-2017-04-17 21.57.41.412.png](/static/assets/img/posts/mwdc/mwdc-2nd-014.png)