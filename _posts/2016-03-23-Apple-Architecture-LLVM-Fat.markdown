---
layout: post
title:  "[iOS] Architecture, LLVM, LIPO"
date:   2016-03-23 01:00:00
tags: objc ios LLVM FAT LIPO Architecture
categories: devstory
---
# Architecture in XCode Build Setting

## iOS Architecture

> Xcode Build Setting > Architecture > armv7s, armv7, arm64


### [ 아키텍쳐 별 iOS Devices ]
- armv7s : iPhone 3GS, iPad(2010), iPhone 4, iPod touch, iPad2, iPhone 4S, (new)iPad, iPad mini
- armv7 : iPhone 5, iPad(2012), iPhone 5c
- arm64 : iPhone 5S, iPad Air, iPad mini 2, iPhone 6, iPhone 6 plus, iPad Air 2, iPad mini3


#### [ 참고 ]
그렇다면 i386, x64 architecture 는?
    - For MacOS 용... 시뮬레이터를 돌리는데 필요한 아키텍쳐이다.

> Q. 아키텍쳐별로 instruction set / structure가 모두 다른데, 어떻게 한 코드로 빌드를 할 수 있는 것일까?
> A. `LLVM` 덕분 !

[![iOS upport Matrix](https://raw.githubusercontent.com/karl-park/karl-park.github.io/57da02b3dc27b70667c820753ff1c5585e543421/assets/images/architecture/21754547448_8c8985d6dc_z.jpg)](http://iossupportmatrix.com/)


## LLVM (Low-Level Virtual Machine)

기존의 컴파일러 구성요소(Frontend, Optimizer, Backend)를 분리해서 각각의 부분들을 조합할 수 있게 디자인 된, 가상 머신.

IR(Intermediate Representation, 중간코드, 비트코드)를 생성. Frontend에서 비트코드를 생성하고, Backend에서는 비트코드를 받아서 타겟에 맞는 결과물을 생성.

![Compiler Structure](https://raw.githubusercontent.com/karl-park/karl-park.github.io/57da02b3dc27b70667c820753ff1c5585e543421/assets/images/objectivec2.0/compiler.PNG)


즉, 비트코드만 생성하는 Frontend만 개발하면, 어느 머신/아키텍쳐에 사용되건, 언어의 종류는 상관없다.


#### [ 참고 ]
> Q. clang이란 무엇인가?
> A. C 계열 Language, C, C++, Objective-C 와 같은 LLVM의 Frontend를 말한다.

## Fat 타입
### Fat 타입 확인
여러가지 CPU 아키텍쳐의 라이브러리가 한 파일에 포함되어 있는 파일

```shell
$ file mylibrary.a 
> mylibrary.a: Mach-O universal binary with 3 architectures
> mylibrary.a (for architecture i386):    current ar archive random library
> mylibrary.a (for architecture armv7):   current ar archive random library
```

위의 경우처럼 `file` 명령어를 사용하면, 원하는 라이브러리가 Fat타입인지 확인할 수 있다. 
위의 경우에서는 i386, armv7이 지원되고 있음을 알 수 있으며, `i386은 시뮬레이터용`, 나머지 `armv7은 실제 기기에서 돌아가는 앱용` 임을 알 수 있다.

### Fat 타입 라이브러리 만들기
라이브러리를 만들기 위해서는 우선, 각 CPU 아키텍쳐별로 빌드된 라이브러리들이 존재해야한다. 예를 들어 위의 경우에서는 i386, armv7으로 빌드된 라이브러리가 있어야한다. 그리고, 다음과 같은 명령어로 Fat 파일을 만들 수 있다.

```shell
$ lipo -output mylibrary.a -create mylibrary_i386.a mylibrary_armv7.a
```

- lipo : OS X에서 유니버셜 바이너리를 관리하기 위한 툴. lipo를 이용해서 fat 타입 라이브러리를 만들 수 있을 뿐만 아니라, 유니버셜 실행파일로부터 한 아키텍쳐를 지원하는 실행파일을 추출할 수도 있다.

Fat 참고 URL : http://www.cocoadev.co.kr/87