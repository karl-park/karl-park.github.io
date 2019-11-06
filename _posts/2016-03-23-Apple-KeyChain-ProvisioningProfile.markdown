---
layout: post
title:  "[iOS] Code Signing, KeyChain, Provisioning Profile"
date:   2016-03-23 05:00:00
tags: objc ios Provisioning Profile KeyChanin Certificate
categories: devstory
---
iOS 개발을 하다보면, 꼬옥 한번씩은 머리를 쥐어짜게 하는 그것. 바로 프로비저닝과 인증서다.
뭐, 이렇게 한번 정리를 해놓아도 (사실 할때마다 까먹어서, 다시 공부하는게 반복.. 되길래 이렇게 블로그에 남겨놓는다) 계속 까먹기에 !! 자주자주 해보고 머리속에 콩! 박아놔야할 것 같다.

# 이 모든 것은 APPLE 의 손아귀 안에
iPhone에서 실행되는 모든 앱은(탈옥제외) 매번 앱이 실행될 때 마다, 애플로부터 인증을 받았는지, 그리고 앱을 실행할 수 있는 권한이 주어졌는지를 확인한다.

그래서 결론은 ! `애플`만이 iOS 디바이스에 대하여 `앱`실행을 `허가` 할 수 있다.

그럼, 우리들은? 우리 개발자들은 어떻게 디바이스에서 테스트하는 걸까? 애플은 우리에게 iOS 디바이스 사용권을 "판매"한다. 나쁘게 말하면 판매이고, 좋게 말하면, `"개발자"를 신뢰하는 방식`으로 "앱실행" 권한을 부여해준다.

## Apple 인증서 (= 개발자 등록)

그럼 어떻게 그 권한을 부여받을 수 있을까? 다음과 같은 순서를 따르면 된다.

1. KeyChain 접근 앱에서 `Certificate Signing Request(CSR)를 생성`한다.
    - KeyChain 접근 앱은 공개키/개인키를 자동으로 생성한다. (잃어버리게 되면, 본인을 인증 할 수 없어서, 앱스토어에 배포를 할수도, 아무것도 할수가 없다 !!)
    -  공개키/개인키를 통해서 CSR 파일을 생성한다. CSR은 개발자의 이름, 이메일, 공개키를 포함하고 있고, 개인키를 이용하여 사인이 된다.

2. Apple Developer Program의 개발자 센터에서 인증서를 받는다.
  - CSR을 Apple에 업로드하면서 인증서를 요청하면, 간단한 확인 작업을 거친 후, `개발 인증서`를 발급해준다.

3. 위의 `개발 인증서`를 받아서, `KeyChain 앱에 등록`하게 되면, My Certificate 카테고리에 추가된다.
  - 해당 인증서는 이 인증서가 애플을 통해서 발급되었고, 애플이 등록한 개발자를 신뢰한다는 정보를 담고있다.
  - 이 인증서는 App을 Signing 할 때 사용된다.


#### [ 참고 ] 키체인(Keychain)앱과 내인증서(My Certificate)
- 키체인(Keychain)앱 : 맥 > 파인더 > 응용프로그램 > 유틸리티 > “키체인접근” 열기
- 열린 창의 왼쪽 하단 카테고리에서 “내 인증서(My Certificate)”


## Provisioning Profile (= 신뢰하는 개발자와 설치 허락 Device의 매칭)

인증서까지 받았다면, 이제 끝난 것 같겠지만, 아직 끝이 아니다.

`개발자`와 `애플`사이의 신뢰 관계(?!)는 인증서로 커버가 되었지만, `개발자`와 `디바이스`사이의 신뢰 관계는 아직이다.

이 때, **`프로비저닝 프로파일`**이 그 역할을 맡아서 처리해준다.

Provisioning Profile 역시 Apple Developer 사이트에서, "인증서"와 "AppID", 그리고 "Device"를 선택하여 다운로드 받을 수 있다.

- Provisioning Profile 종류
    - 개발용 프로파일 : 개발 테스트 목적으로 준비된 프로파일.
    - 배포용 프로파일 : 임시배포용(Ad-Hoc)과 App Store 등록용 프로파일로 구분. 배포용 인증서는 에이전트만 할 수 있다.

### Team Provisioning Profile

- Team Provisioning Profile
    - AppID
        - Name
        - Search String
        - Services
    - Certificates
        - Development Certificate
            - Public Key    
        - ...
    - Devices
        - Device
            - Name
            - DeviceID
        - ...

#### [참고] Team Member Group
- Team Agent : 최초 Team Admin, 애플과의 계약서에 사인한 Admin, 모든 법적인 책임. 배포용 프로비저닝 프로파일 관리
- Team Admin : 다른 Admin/Member 추가/관리, 멤버로부터의 인증서 발급신청 승인, 디바이스 등록
- Team Member : 개발용 인증서 신청, 프로비저닝 파일을 이용해 개발장치에 앱을 인스톨 가능
- 개인개발자 : All Position

## Build -> Install to the Device
- App에 포함된 Provisioning Profile이 애플에서 서명된 것인지 확인
- CodeResources라는 파일에 기록된 해쉬정보를 실제 파일들과 비교해서, 빌드 후, 수정이 되지 않았음을 확인
- 앱이 변조되지 않았음을 확인
- 앱에 포함된 프로비저닝 프로파일과같은 실제프로비저닝 프로파일을 가지고 있는지 확인
- 실행

#### [참고] Team Member Group
- 실제프로비저닝 프로파일 : 컴파일 때 사용한 프로비저닝 프로파일의 복사본
- CodeResources : _CodeResources라는 폴더에 들어있으며, 단순한 plist파일이지만, 모든 파일의 암호화된 해쉬정보를 가지고 있다.


# Reference
- http://korea-developer.tistory.com/entry/iOS-Provisioning-profile-%EB%A7%8C%EB%93%A4%EA%B8%B0
- http://la-stranger.blogspot.kr/2014/04/ios.html
- http://theeye.pe.kr/archives/761
- http://funnyrella.blogspot.kr/2014/04/98-ios.html
- http://samablog.tistory.com/64