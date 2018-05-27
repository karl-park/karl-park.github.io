---
layout: post
title:  "[Objective-C] Runtime, Structure"
date:   2016-03-21 00:00:00
tags: objc ios Runtime Structure Architecture
categories: devstory
---


# Objective-C 런타임 & 오브젝트 구조
개발자에게 `런타임`이라는 단어를 들려주면, 먼저 무언가 `동적(Dynamic)`이다라는 느낌이 든다고 말을 할 것이다. 

이렇듯, Objective-C는 런타임이 있으므로, 동적인 언어라고 말할 수 있다. 

정적인 언어는 컴파일/링크 시점에서 어떠한 코드들이 어떻게 실행될 지(어떤 객체가 어떤 메소드를 콜할지)를 결정하지만, 런타임을 가진 언어는 실행시점에 그것을 판단하므로, 동적이고 유연하다고 말할 수 있겠다.

## Objective-C 런타임 코드
Objective-C 의 런타임은 Apple이 오픈소스로 공개하고 있다. ([링크](http://opensource.apple.com)) 

이러한 런타임 클래스 정보는 다음과 같다.
- `objc_class` : Objective-C의 클래스
- `objc_object` : 클래스로부터 생성된 객체 --> typedef --> id 포인터
- `isa` : 모든 objc_object가 가지는 클래스 변수

```objc
// objc.h
typedef struct objc_class *Class;
typedef struct objc_object {
    Class isa;
} *id;
```

따라서, `id` 포인터를 이용하게 되면, 해당 포인터가 지칭하고 있는 객체가 어떤 클래스인지, 어떠한 메소드를 가지고 있는지를 알 수 있다.


## Objective-C 오브젝트 구조
Objective C 에서의 Method Call을 흔히 Message Sending 이라고 한다. 이렇게 `메시지 전달` 이라는 조금은 독특한 개념으로 접근하는 것은 여러 의미가 있는데, 그 중, 오늘은 오브젝트 구조에 대해서 살펴보고자 한다.

Java와 비교해서 Objective C의 오브젝트 구조는 조금은 특이하다. 흔히 Java는 클래스와 인스턴스라는 크게 2가지 개념으로 접근 할 수 있는데, Objective C는 인스턴스 오브젝트, 클래스 오브젝트, 그리고 메타 클래스 오브젝트로 나뉜다.

```objc
NovelBook *myNovelBook = [[NovelBook alloc] init]; 
```

위 코드로 생성한 `myNovelBook`은 어떻게 생성이 될까?

Class Object인 NovelBook을 이용해서 myNovelBook을 생성하게 되는데, 이 때의 오브젝트 구조는 다음 그림과 같다.
![objective c object structure](https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/objectstructure/objectstructure.png)

위에서 설명한 `런타임`은 가장 먼저 `myNovelBook의 isa 포인터를 이용`해서 NovelBook 포인터를 얻게 된다. 그 후, NovelBook Class Object 객체에서 alloc 메시지를 보내게 된다. 만약, NovelBook Class에 alloc 메소드가 구현되어 있지 않다면, superclass 포인터를 따라서, 부모 클래스를 재탐색 하게 된다. 이러한 `체이닝`을 통해서, 메시지는 전송되게 되는 것이다.


- 참고 URL : http://www.letmecompile.com/objective-c-런타임runtime-내부-동작-분석/#fn:2