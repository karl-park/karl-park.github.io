---
layout: post
title:  "[Objective-C-2.0] 책요약 03"
date:   2016-03-22 03:00:00
tag:
- iOS
- Objective-C
- Objective-C 2.0
- 책요약
ios: true
---

![](https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/4b664436fed33ae28727acb212ff6127092a3b82/assets/images/objectivec2.0/objectivec2.0.PNG)

# 책 요약 본

## Table of Index

- 01장 Hello Objective C
- 02장 C의 확장
- 03장 객체지향프로그래밍의 소개
- 04장 상속
- 05장 컴포지션(Composition)
- 06장 소스 파일 구성
- 07장 Xcode에 대하여
- 08장 Foundation Kit 소개
- 09장 메모리 관리
- 10장 객체 초기화
- 11장 프로퍼티
- `12장 카테고리`
- `13장 프로토콜`
- `14장 AppKit 소개`
- `15장 FileIO`
- `16장 키-밸류 코딩 KVC`
- `17장 NSPredicate`

# 12장 카테고리
- Dynamic runtime dispatch : 기존의 클래스에 메소드를 런타임에 추가 == `Category`

## 좋은 카테고리
- 클래스의 구현을 여러 파일이나 여러 플랫폼으로 나눌 때
- private method 의 전방참조를 만들 때
- 객체에 비공식 프로토콜을 추가할 때

## 나쁜 카테고리
- 인스턴스 변수를 새로 추가할 수 없다.
- 카테고리 메소드가 기존 이름과 충돌시, 카테고리가 우선. 기존 메소드를 부를 길이 없음.

## 카테고리로 전방 참조 만들기(forward reference)
- private한 메소드들을 카테고리에서 선언만 함으로써, 컴파일러로 부터의 경고를 피할 수 잇다. 실제 구현은 굳이 해당 카테고리안에서 할 필요는 없다.

## 비공식 프로토콜과 델리게이션 카테고리
- `delegate` : 다른 객체로부터 그 객체의 특정한 일을 대신 해달라는 요청을 받는 객체
- `비공식 프로토콜(informal protocol)` : NSObject에 카테고리를 놓는 것.

## 셀렉터에 응답하기
- `셀렉터(selector)` : Objective-C 런타임이 빠른 검색에서 사용하는 특별한 방식으로 인코딩되어 있는 메소드.
- 메소드의 인수로 넘겨질 수 있고 / 인스턴스 변수로 저장도 가능하다.

# 13장 프로토콜
- **공식 프로토콜(formal protocol)** *VS* **비공식 프로토콜(informal protocol)**
- 공식 프로토콜 : 지정된 메소드의 목록. 프로토콜에 따른다.(conform) (== 프로토콜의 모든 메소드를 구현하겠다라는 의미)` == Java의 인터페이스 !!!`
- 비공식 프로토콜 --> @optional 을 통해 구현

## 프로토콜 선언
```objectivec
@protocol NSCoding
- (void) encodeWithCoder: (NSCoder *) aCoder;
- (id) initWithCoder: (NSCoder *) aDecoder;
```
- 위와 같이, @protocol 지시자를 붙여준 후, 프로토콜을 정의한다.

## 프로토콜 채택
```objectivec
@interface Car : NSObject <NSCopying(, NSCoding, ...)>
{
	... // instance variables
}
... //methods
@end	// Car
```
- 위와 같이, @interface 뒤, 클래스 선언의 괄호안에 나열하면 된다.

## 사본 만들기
- shallow copy : 참조하는 객체를 복사하지는 안혹, 그저 이미 존재하는 참조된 객체를 가리키기만 한다.
- deep copy : 참조하는 모든 객체의 사본을 만든다.
- 
### Engine 복사(deeply)

```objectivec
@interface Engine : NSObject <NSCopying>
@end // Engine

@implementation Engine
	// ...
- (id) copyWithZone: (NSZone *) zone
  {
	Engine *engineCopy;
    engineCopy = [[self class]
    							allocWithZone: zone]
                                init];
	return (engineCopy);
  }
@end	
```

- 으응 ? 저 `[self class]` 는 왜하는거지? 그냥 `[Engine allocWithZone]`을 하면 안되나? 라는 질문을 할 수도 있겠다.
- 저렇게 하는 이유는 !!!! 저렇게 하지않으면 *서브클래스*에서는 저 코드가 동작하지 않기 때문이다.

## @opitional, @required 를 통해서, 좀더 명확히 !