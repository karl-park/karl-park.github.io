---
layout: post
title:  "[Objective-C] Objective-C-2.0 책요약 02"
date:   2016-03-22 02:00:00
tags: objc ios Summary 책요약
categories: devstory
---

이전 편에 이쳐서 책(Objective-C 2.0)을 요약하는 포스팅을 올리려고한다.

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
- `08장 Foundation Kit 소개`
- `09장 메모리 관리`
- `10장 객체 초기화`
- `11장 프로퍼티`
- 12장 카테고리
- 13장 프로토콜
- 14장 AppKit 소개
- 15장 FileIO
- 16장 키-밸류 코딩 KVC
- 17장 NSPredicate

# 08장 Foundation Kit 소개

- 범위를 나타내는 구조체 : NSRange

```objc
typedef struct _NSRange{
    unsigned int location; unsigned int lenght;
} NSRange;
```

- 기하학 관련 타입구조체 : NSPoint, NSSize, NSRect

```objc
typedef struct _NSPoint{
    float x; float y;
} NSPoint;

typedef struct _NSSize{
    float width; float height;
} NSSize;

typedef struct _NSRect{
    NSPoint origin; NSSize size;
} NSRect;
```

- 클래스 메소드 : + 기호로 선언한 메소드. 주로 클래스의 새로운 인스턴스를 만드는데 사용된다.
- 팩토리 메소드 : 새로운 객체를 만드는데 사용되는 클래스 메소드(ex) stringWithFormat)
- NSString : Java-String => immutable
- NSMutableString : Java-StringBuffer => mutable

# 09장 메모리 관리

## 객체의 Life Cycle

### 참조횟수 : reference counting(retain counting)

- alloc, new, copy : retainCount = 1;
- retain : retainCount++;
- release : retainCount--;
- dealloc : if(retainCount==0)

### 객체 소유권
특정 객체가 다른 객체를 가리키는 인스턴스 변수를 가지고 있다면, 객체를 소유하고 있다고 말할 수 있다. 이 때, 2개 이상의 retainCount 값을 가지게 되면, 우리는 `조심히` 이를 다뤄야한다. (함부로 dealloc이 되면 안되기 때문이다. 반대로 잘못 retain하게 되면 memory leak이 발생한다.)

### Autorelease
- NSAutoreleasePool : NSMutableArray를 사용해서 객체들을 추가하고, dealloc 메소드 안에서 배열에 있는 모든 객체에 release 메시지를 보내도록 만들어져 있다. 이 오토릴리즈풀은 스택으로 유지되기에 큰 부담이 없다.

# 10장 객체 초기화

- 객체 할당 방법
    - new
    - alloc / init

## 객체 할당
- 할당(allocation) : OS로부터 일정 메모리를 *할당*받은 후, 객체의 인스턴스변수들을 가지고 있는 위치가 그 메모리에 덧씌워져서, 실제로 그 객체가 메모리 주소를 갖게 되는 것.
- alloc 메시지와 초기화
    - BOOL : NO
    - int : 0
    - float : 0.0
    - *(pointer variable) : nil

## 객체 초기화
- 초기화(initialization) : 일정 메모리를 얻어와 객체 단체에 있어서 생산적인 멤버로 준비시킨다.

```objc
Car *car = [[Car alloc] init];        // OK

Car *car = [Car alloc]                // DO NOT !!
[car init]
```

- 위의 두 초기화 방법 중, 두번째 방법은 피하는게 좋다.(피해야한다) 왜냐하면, alloc 과정에서 실제로는 다른 클래스, 예상치 못한 클래스가 할당될 수도 있기 때문이다.(예를 들면 클래스 클러스터 같은 경우) init 메소드는 인수를 받을 수 있으므로, 받아들인 인수를 살펴보고 더 적절한 객체의 다른 클래스를 선택할 수 있기에, alloc 과 init을 같이 사용하도록 하자 !

# 11장 프로퍼티
- @property/@synthesize : 전처리기
    -> Build and Run 을 하였을 때, prewritten/preformatted 코드 블락으로 대체 !!
- 즉, getter / setter 를 자동적으로 생성해준다.

## @property

### @interface 에서 사용

### Syntax

- @property(attributes [, attribute2, ...]) type name;

- (attributes [, attribute2, ...])
    - getter=getterName - getter의 이름을 getterName로 지정
    - setter=setterName - setter의 이름을 setterName로 지정
    - readwrite - 기본동작으로 getter와 setter를 모두 만듦. Mutually exclusive로 readwrite
    - readonly - getter만 만듦. Mutually exclusive로 readwrite. 값을 할당하려고 하면 컴파일 오류 발생
    - assign - 기본동작, setter가 간단한 할당을 사용(예 location = where;) 객체를 소유할 필요가 없을때 사용
    - retain - assign과 비슷하지만 레퍼런스 카운트를 증가. Mutually exclusive로 assign과 copy. 포인터객체를 할당할 경우에는 외부에서 객체가 릴리즈되어 파괴된 객체를 참조하는 문제를 막기 위해서 클래스가 멤버객체를 소유하도록 레퍼런스카운트를 증가(이전 값을 release)
    - copy - 할당하는데 객체의 복사본을 사용. Mutually exclusive로 assign과 retain. 포인터객체의 경우 레퍼런스의 값이 바뀌어 프로퍼티의 값이 바뀌는 걸 막기 위해 setter에서 복사본을 만들어서 할당하며 copy를 사용하려면 NSCopying 프로토콜을 구현한 객체에서만 유효
    - nonatomic - 엑세서들을 non-atomic으로 지정. 멀티쓰레드 환경에서 사용하지 않는 한 접근자를 더 빠르게 동작하게 해준다. Mutually exclusive락으로 접근자 메서드를 보호하지 말라고 지시하는 것. atomic이 기본동작.

## @synthesize

### @implementation 에서 사용

### Syntax

- @synthesize name;

## @dynamic

### @implementation 에서 사용

- @synthesize대신에 사용할 수 있으며 getter와 setter메서드가 클래스 자신에 의해서 구현되지 않고 (슈퍼클래스같은) 다른 어딘가에 구현되어 있다고 알려주어 getter/setter가 구현되어 있지 않아도 컴파일러 경고를 받지 않도록 해줌.

### Syntax

- @dynamic name;
