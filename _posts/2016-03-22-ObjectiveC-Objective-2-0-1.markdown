---
layout: post
title:  "[Objective-C] Objective-C-2.0 책요약 01"
date:   2016-03-22 01:00:00
tags: objc ios Summary 책요약
categories: devstory
---

이번글을 포함하여 3편에 걸쳐서 아래책(Objective-C 2.0)을 요약하는 포스팅을 올리려고한다.

- 1편 : [Objective-C-2.0 책요약 01](../ObjectiveC-Objective-2-0-1/)
- 2편 : [Objective-C-2.0 책요약 02](../ObjectiveC-Objective-2-0-2/)
- 3편 : [Objective-C-2.0 책요약 03](../ObjectiveC-Objective-2-0-3/)

![](https://raw.githubusercontent.com/karl-park/karl-park.github.io/4b664436fed33ae28727acb212ff6127092a3b82/assets/images/objectivec2.0/objectivec2.0.PNG)

# 책 요약 본

## Table of Index

- `01장 Hello Objective C`
- `02장 C의 확장`
- `03장 객체지향프로그래밍의 소개`
- `04장 상속`
- `05장 컴포지션(Composition)`
- `06장 소스 파일 구성`
- 07장 Xcode에 대하여
- 08장 Foundation Kit 소개
- 09장 메모리 관리
- 10장 객체 초기화
- 11장 프로퍼티
- 12장 카테고리
- 13장 프로토콜
- 14장 AppKit 소개
- 15장 FileIO
- 16장 키-밸류 코딩 KVC
- 17장 NSPredicate

# 01장 Hello Objective C
## Objective-C 의 탄생
1980년대 초, Brad Cox에 의해서 탄생
C 언어의 superset

# 02장 C의 확장
## 가장 간단한 오브젝티브-C  프로그램

```objc
#import <Foundation/Foundation.h>
int main(int argc, const char* argv[]){
    NSLog(@"Hello, Objective-C!");
    return (0);
}
```

- `#import` : 헤더파일의 중복 포함을 막기위해서 C언어에서는 "#include + #ifdef"를 사용하고 있는데 반해, Objective-C 에서는 "#import"를 사용하고 있다.

- `#import <Foundation/Foundation.h>` : Foundation 프레임워크에서 Foundation.h 헤더파일을 찾아, 중복을 제거한 후, #include 하라는 뜻
- `프레임워크` : 여러부분(header, library, image, sound, etc...) 등이 모여서 하나의 단위로 묶여있는 컬렉션(ex)  코코아(Foundation+ApplicationKit(UIKit)), 카본, 퀵타임, OpenGL 등)
- `NSLog에서의  NS` : NextSTEP을 위해 이미 작성된 코드와의 호환을 유지하기위한 접두사.
- `YES/NO` : #define에 정의되어있고, 각각 true/false 를 의미 --> BOOL 의 리턴 타입
- `@` : C언어의 확장이라는 표시(ex) @interface, @Implementatoin, @property ...)

# 03장 객체지향프로그래밍의 소개
## 모든길은 *인다이렉션*으로 통한다
- `Indirection(인다이렉션)` : 코드에 있는 값을 바로 사용하지말고 그 값을 가리키는 포인터를 사용하라. (~= delegation !?)
- 코드상 여러개를 고칠 것을 하나의 변수로, 하나의 창구로 통일하면, 유지보수에 용이하지 않을까?!!!

- 객체지향 vs 절차지향
    - 절차지향 : 함수 주위를 데이터가 맴돈다.
    - 객체지향 : 데이터 주위를 함수가 맴돈다.
(? 와닿지는 않지만, 조금 와닿는 뭐지... 이 미묘한 깨달음은?)

- `id` : identifier, 어떤 종류의 객체든 참조할 수 있는 일반적인 타입.
- `[receiverObject selecor]` : Method Call, 즉 `메세지 호출`(Message Send)
- `Class`(클래스) : 객체의 타입을 나타내는 구조체.
- `Object`(객체) : 값과 클래스를 가리키는 숨어있는 포인터를 갖는다.
- 'Instance`(인스턴스) : 객체의 또다른 말
- `Message` : 객체가 수행하는 액션
- `Method` : 메시지에 반응하는 코드
- `Method Dispatcher` ; 특정 메시지에 어떤 메소드가 반응하게 되는지를 알기위해 Objective-C가 사용하는 방법
- `Interface` : 개겣의 클래스에 의해 제공되는 내용의 설명
- `Implementatoin` : 인터페이스가 동작하도록 하는 코드
- `Runtime` : 사용자가 프로그램을 실행할 때 애플리케이션을 지원하는 코드덩어리. 객체에 메시지를 보내고, 파라미터를 전달하는 것과 같은 중요한 작업을 수행한다.(9장 이후로 자세한 설명)


# 04장 상속

## Shape를 상속받는 Circle/Triangle/Rectangle 을 구현해보자.

- 각 클래스는 x/y/width/height를 가지는 구조체 ShapeRect와 색상을 가지는 ShapeColor 구조체를 멤버로 가진 후, 각각에 대한 setter/getter, 그리고 그릴 수 있는 draw 메소드를 가진다

> Do it !

## 상속 문법

```objc
@interface BaseClass : NSObject
...

@interface DerivedClass : BaseClass
...
```

## Method Dispatching
- isa 포인터와 super 포인터를 이용해서 method를 찾는다. 우선적으로는 selector와 그에 상응하는 method 를 dispatcher table에서 찾는다.

# 05장 컴포지션(Composition)
- 상속과의 차이를 알아두면 좋을 듯...
- `상속` : **is a** 관계
- `컴포지션` : **has a** 관계


# 06장 소스 파일 구성
- `.h` : 헤더파일, @interface 키워드로 인터페이스 부분을 알려준다. 클래스의 메소드 호출, 다른 클래스와의 컴포지션, 서브클래스 만들기 등을 보여준다. `클래스의 선언`
- `.m / .mm` : @implementation 을 통해서, 클래스의 실제 동작을 보여주며, 인터페이스에서 선언된 메소드를 구현하는 코드를 보여준다. `클래스의 정의`
- '@class` 키워드 : 이것은 클래스이므로 포인터를 통해서 참조하려고한다. 라는 것을 컴파일러에게 알려준다.
    - forward reference : 전방 참조 - 컴파일러에게 "이봐 컴파일러, 나를 믿어봐. 나중에는 이 클래스가 어떤 것인지 알게 될테니까. 지금은 이것으로 처리해줘."
    - circular dependency : 상호 참조 - 클래스A가 클래스B를 사용하고, 클래스B 또한 클래스A를 사용할 때, 서로 #import 를 시키면 컴파일 에러가 발생한다. @class를 통해서 해결이 가능하다.


# 07장 Xcode에 대하여
- 생략 (하면서 익히도록 !!)