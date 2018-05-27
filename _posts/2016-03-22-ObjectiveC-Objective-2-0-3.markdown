---
layout: post
title:  "[Objective-C] Objective-C-2.0 책요약 03"
date:   2016-03-22 02:00:00
tags: objc ios Summary 책요약
categories: devstory
---

이전 편에 걸쳐서 책(Objective-C 2.0)을 요약한 글이다.
이번편이 마지막이다 ^^

- 1편 : [Objective-C-2.0 책요약 01](../ObjectiveC-Objective-2-0-1/)
- 2편 : [Objective-C-2.0 책요약 02](../ObjectiveC-Objective-2-0-2/)
- 3편 : [Objective-C-2.0 책요약 03](../ObjectiveC-Objective-2-0-3/)

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
```objc
@protocol NSCoding
- (void) encodeWithCoder: (NSCoder *) aCoder;
- (id) initWithCoder: (NSCoder *) aDecoder;
```
- 위와 같이, @protocol 지시자를 붙여준 후, 프로토콜을 정의한다.

## 프로토콜 채택

```objc
@interface Car : NSObject <NSCopying(, NSCoding, ...)>
{
    ... // instance variables
}
... //methods
@end    // Car
```

- 위와 같이, @interface 뒤, 클래스 선언의 괄호안에 나열하면 된다.

## 사본 만들기

- shallow copy : 참조하는 객체를 복사하지는 안혹, 그저 이미 존재하는 참조된 객체를 가리키기만 한다.
- deep copy : 참조하는 모든 객체의 사본을 만든다.

### Engine 복사(deeply)

```objc
@interface Engine : NSObject <NSCopying>
@end // Engine

@implementation Engine
    // ...
- (id) copyWithZone: (NSZone *) zone
  {
    Engine *engineCopy;
    engineCopy = [[self class] allocWithZone: zone] init];
    return (engineCopy);
  }
@end    
```

- 으응 ? 저 `[self class]` 는 왜하는거지? 그냥 `[Engine allocWithZone]`을 하면 안되나? 라는 질문을 할 수도 있겠다.
- 저렇게 하는 이유는 !!!! 저렇게 하지않으면 *서브클래스*에서는 저 코드가 동작하지 않기 때문이다.

## @ opitional, @ required 를 통해서, 좀더 명확히 !

# 14장 AppKit 소개
- Java Swing 과 비슷하다

# 15장 FileIO

## 객체 인코딩하기

- Encoding, Decoding, Serialization, Deserialization

## FileIO 2가지 방법

1. Property List Data Type - 흔히 plist 라고 불리움
    - NSArray, NSDictionary, NSString, NSNumber, NSDate, NSData, etc.

```objc
    NSArray *phrase;
    phrase = [ NSArray arrayWithObjects: @"I", @"seem", @"to",
                                @"be", @"a", @"verb", nil ];
    [ phrase writeToFile: @"/tmp/verbiage.txt" atomically: YES ];
```

2. NSCoding 프로토콜을 채택하고, 메소드를 구현.

```objc
-(void) encodeWithCoder: (NSCoder *) coder {
    [ coder encodeObject: name
            forKey: @"name"];
    [ coder encodeInt: age
            forKey: @"age"];                
    ......
} // encodeWithCoder

-(void) initWithCoder: (NSCoder *) decoder {
    if ( self = [ super init ] ){
        self.name = [ decoder decodeObjectForKey: @"name" ];
        self.age = [ decoder decodeIntForKey: @"age" ];
        ...        
    }
    
    return (self);
} // initWithCoder

...
NSData *freezeDried;
freezeDried = [ NSKeyedArchiver archivedDataWithRootObject: myObject ];
// archivedDataWithRootObject가 NSKeyedArchiver 인스턴스를 생성한 후,
// myObject:encodeWithCoder 인자로 보낸다. myObject 객체 전체가 키와 값으로
// 인코딩되고 난 후, 아카이버는 모든 것을 NSData로 만들어서 반환한다.

[ freezeDried writeToFile: @"/tmp/myObject.txt" atomically: YES ];
// 를 통해서 파일저장도 가능하다.

[ myObject release ];
myObject = [ NSKeyedUnarchiver unarchiveObjectWithData: freezeDried ];
// 위으 메소드를 통해서 아카이빙한 데이터에서 객체를 재생성한다.
```


# 16장 키-밸류 코딩 KVC

## KVC 소개

- \-valueForKey:
- \-setValue:forKey:
- \-valueForKeyPath:
- \-dictionaryWithValuesForKeys:
- \-setValuesForKeysWithDictionary:

# 17장 NSPredicate

- 비교 조건 필터 클래스

```objc
Car *car = makeCar (@"Herbie", @"Honda", @"CRX", 1984, 2, 110000, 58);
[ garage addCar: car ];

NSPredicate *predicate;
predicate = [ NSPredicate predicateWithFormat: @"name == 'Herbie'" ];

BOOL match = [ predicate evaluateWithObject: car ];
NSLog (@"%s", (match) ? @"YES" : @"NO" );

predicate = [ NSPredicate predicateWithFormat: @"engine.horseposer > 150" ];
match = [ predicate evaluateWithObject: car ];
NSLog (@"%s", (match) ? @"YES" : @"NO" );

NSArray *cars = [ garbage cars ];
for( Car *car in [ garage cars ] ){
    if(predicate = [ predicate evaluateWithObject: car ]){
        NSLog (@"%@", car.name );        
    }
}

// next predicate is also possible
predicate = [ NSPredicate predicateWithFormat: @"engine.horseposer BETWEEN { 50, 150}" ];
```
