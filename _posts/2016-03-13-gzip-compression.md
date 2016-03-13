---
layout: post
title:  "gzip"
date:   2016-03-13 12:00:00
tags: gzip apache compression
---
안녕하세요 ?!
오늘은 웹서비스 최적화 기법인 gzip 압축전송에 대한 것을 나누고자 합니다 !

## 순서
1. gzip compression 의 등장 배경
2. gzip 적용
3. gzip 적용 Before/After
4. Reference URL

## gzip compression의 등장 배경
- gzip은 이름만 보아도, 무엇인가를 "압축"하리라는 것을 알 수 있습니다.
웹서비스를 하게되면, 많은 양의 document들을 만나게 될텐데요, 이들 모두를 클라이언트에게 통채로 전달하는 것은 어쩌면 비효율적이라고 할 수 있습니다. 요청수가 늘어날 수록, 네트워크 부하량이 크게 증가하리라는 것은 당연하구요.
- 그래서 서버의 출력을 네트워크로 클라이언트에 보내기 전에 ! 압축을 해서 보내는 필터를 생각해내게 됩니다. 그것이 바로 gzip compression이라고 생각하시면 됩니다 !

## gzip compression 적용 (엄청 간단해요!)
- gzip compression 을 적용하기 위해서는 우선, 몇가지 조건사항이 필요한데요, 그것은 다음과 같습니다.
클라이언트의 브라우저에서 gzip을 지원하여야합니다. 아래 캡쳐된 사진과 같이, Request Headers의 Accept-Encoding에 gzip이 있으면 됩니다 !
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/gzip%20browser.png ]]
서버단에서 클라이언트에게로 gzip으로 압축하여 보내는 모듈이 필요합니다.

---
- 그렇다면, gzip 적용방법을 알아볼까요? 이번 기술공유에서는 apache에 mod_delflate 모듈을 얹어서 gzip을 적용해보도록 할게요 !
1. 서버에 접속하여 apache의 설정파일을 편집합니다. 저는 다음과 같은 경로의 httpd.conf 를 열었네요.
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/vi%20%EC%97%B4%EA%B8%B0.PNG ]]

2. httpd.conf에서 LoadModule deflate_module modules/mod_deflate.so 를 검색하여, apache에 deflate 모듈이 적용되어있는지를 확인합니다. 만약 모듈이 안올라와있다면, 따로 설치를 해주시겠어요?
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/LoadModule%20deflate.PNG ]]

3. httpd.conf 마지막 부분에, 다음과 같이 코드를 입력해줍니다. 
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/IfModule%20mod_deflate.png ]]

4. 설정을 적용하셨다면 저장을 하신 후, 아파치를 재시작해 줍니다 !
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/apachectl%20graceful.PNG ]]

5. 자 이제 브라우저에 들어가서 ! 한번 비교해보실까요?!
( compression을 적용하기 전/후 를 비교해보시면 더 좋겠습니다. 만약 비교를 놓치신 분들을 위해 !! 밑에 before/after를 준비했어요 !)

## gzip compression 적용 Before/After
### Before
[[ https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/gzip/before_gzip.png ]]
### After
[[ https://github.com/MrKarl/MrKarl.github.io/blob/master/assets/images/gzip/after_gzip1.png ]]
[[ https://github.com/MrKarl/MrKarl.github.io/blob/master/assets/images/gzip/after_gzip.png ]]

## Reference URL
- Apache Module mod_deflate : https://httpd.apache.org/docs/2.2/ko/mod/mod_deflate.html
- gzip wiki : https://ko.wikipedia.org/wiki/Gzip