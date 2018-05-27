---
layout: post
title:  "[Objective-C] Block Syntax"
date:   2016-03-21 03:00:00
tags: objc ios Block Block_Syntax Closure
categories: devstory
---


# Objective-C Block Syntax

## What the Block is?

> 이거슨 무엇인가?

`Block 문법`을 처음 접하곤 엄청나게 당황했었던 나는, 바로 Block에 대해서 검색을 해보았다. 물론 Objective-C 가 C로 컴파일되어서 실행되는 親C 진영의 한 언어라지만, 블록 문법은 C와는 담을 쌓고 살아온 내게 신선함을 안겨다 주었다.(사실 신선함이라기 보단 당황과 황당)

결론부터 말하자면, Block은 `C의 함수`이다. 정확히 말하자면, 런타임에 생성되는 `Dynamic Funciton` 이다.

(Dynamic 이라는 단어를 들으면, `Parallel`, `Asyncronization`이 생각 나는 것은.. 어쩌면 개발자로서 당연한 것이 아닐까. 연관지어서 생각하자 !)

이쯤해서, Apple Developer 의 Block에 관한 아티클 코드를 가져와보도록 하자.


```objc
int multiplier = 7;
int (^myBlock)(int) = ^(int num) {
    return num * multiplier; 
};
```

위의 코드를 보면, 당최 무슨말인지... 헷갈릴만도 한데 ! 하나하나 천천히 분석해보자.

```
1. int : myBlock 이라는 부분은 "Block"으로써, int를 리턴한다.
2. (^myBlock) : "^"를 통해서, myBlock 변수를 블록이 되도록 선언한다.
3. (int) : 파라미터를 나타내며, 그 타입은 int이다.
4. ^(int num) : 파라미터 이름이 num이다.
5. { return num*multiplier; }; : Block의 Body부분으로써, literal block definition 이다. myBlock 에 할당된다.
```

> 오오 신기해 +_+ !!


가만 보면, 어디선가 본 듯한... 이 낯선 친구는 !! 아 ! 그렇다, JavaScript의 `클로저`와 닮아있구나 !!!(***익명함수*** 와도..) 클로저 하면 또 생각나는게 `콜백`이기두 하구 ~~ (*블록 자체가 원래 클로저를 지원하기 위해 만들어졌다고 한다*)

이러한 Block 은 외부의 변수에 대한 `참조`만 가능하다.
물론, 참조하는 값을 변경할 필요가 생기기 마련인데, 그 값을 변경하기 위해서는 변경하고자 하는 변수 선언부에 `__block` 키워드를 붙여주어야 한다!

```objc
__block int x = 0;
void (^increaseX)(void) = (^void) {
    x++; printr("%d", x); 
};
```

외부 변수를 참조만할 때는 그냥 쓰고 !
외부 변수값을 변경하려면 `__block` 키워드를 붙여주자!


> 재밋는 코드를 한번 더 살펴보자

```
int multiplier = 7;
int (^myBlock)(int) = ^(int num) { 
    return num * multiplier; 
};
multiplier = 13;
 
printf("%d\n", myBlock(3));
```

다음의 경우, 어떠한 값이 출력될까?
~~3*13 = 39 가 출력 될 것 같지만~~ 아쉽게도, 
답은 3\*7 = 21 이다.

어떻게 !! 이런 현상이 발생할까?
당연히, Closure개념을 알고 있다면, 감이 잡힐듯하다.

클로저(block)가 선언될 때에는 `선언되는 시점의 값`들이 "stack"에 올라가게 된다. 즉, `선언 당시의 환경`이 중요한 것이다. 위의 예제 같은 경우, multiplier는 myBlock 선언 시, const int 타입으로 스택에 ***복사*** 가 된다. (`캡쳐링`의 개념)

## __block 처리

외부 변수값을 스택에 ***복사*** 해서 쓰는 것 말고, 외부 변수를 직접 ***참조*** 하기 위해서, __block 키워드를 쓴다는 것은 위에서 살펴본바있다. 

그렇다면, __block 처리 로직은 어떻게 이루어지는 것일까?

위에서 설명했듯, 외부 변수를 block scope 내부에서 사용할 때는 block stack에 복사한다고 하였다. 이 때, 다음의 4가지 경우의 수가 있겠다. 그리고 그 처리의 차이는 다음과 같다.

1. 변수가 객체일 경우
    - 객체일 경우 자동으로 retain 한다. 
2. 변수가 객체이고, __block 키워드가 붙은 경우
    - retain 하지 않는다.
3. 변수가 primitive type일 경우
    - const 형태로 value-copy 되어 전달된다.
4. 변수가 primitive type이고, __block 키워드가 붙은 경우
    - reference 형태로 전달된다. 즉, 힙 영역에 복사되고, 그것을 참조한다.


## Warning : Retain Cycle (almost Danger)

다음의 상황을 생각해보자.

클래스가 하나 있고, 그 클래스의 인스턴스가  Block에 대한 참조를 가지고 있다고 하자. 해당 Block 안에서, `self` 를 참조 하는 경우, 어떠한 일이 발생할까?

위의 `Warning` 경고처럼, 위의 상황은 심각한 메모리 누수를 발생시킨다. 왜냐하면, 인스턴스는 block 객체를 retain 시키고, block 객체 안의 self 또한 인스턴스를 retain 시키기 때문이다. 이러한 `순환참조`는 꼭 피하여야 한다. 그렇다면 어떻게 이러한 순환참조를 회피할까?

> 약한 참조를 가지는 객체를 생성하여 넘겨주자.


예제 코드는 다음과 같다.

```objc
@interface BlockWeak : NSObject
@property (copy) void (^block)(void);
@end

@implementation BlockWeak
-(void)exampleCodeBlock {
    self.block = ^ {
        // Capturing the strong reference to self
        // creates a strong reference cycle
        [self doSth];           
    }
}
```

이러한 코드를 다음과 같이 바꿀 수 있다.

```objc
@interface BlockWeak : NSObject
@property (copy) void (^block)(void);
@end

@implementation BlockWeak
-(void)exampleCodeBlock {
    BlockWeak * __weak weakSelf = self;
    self.block = ^ {
        // Capturing the weak reference to avoid the reference cycle
        [weakSelf doSth];
    }
}
```

`오호 !!`

재밋는 녀석이다.