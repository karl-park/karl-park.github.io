---
layout: post
title:  "[Objective-C] Private Method"
date:   2016-04-12 03:00:00
categories: [iOS]
tags: [iOS,Objective-C,Private Method,OODP,Class Continuation]
---

# Objective C 에는 Private Method가 없다?
뭐니뭐니해도, Objective C 를 다루면서, ~~가장 어의없었!!(다기보다는 당황했던것은)던 것은
`Access Modifier`의 존재였다.
접근지정자/접근제한자라고도 불리우는 이것은,
Java와, PHP, Cpp 등의 객체지향 언어들을 사용하며 익숙해졌던 것이었는데...
Objective C 에는 이것이 보이지않아서(사실은 그 철학? 이 녹아져... 있다고 여긴...맞나?)
많이 당황했다.

## 철학이란...
Objective C 는 메소드 디스패치에 있어서 동적인 것에 의존한다. 그 동적인 것, `런타임`에서는
모든 구현된 메소드들이 적나라하게 ~~까발려진다~~(런타임에 의해서 파악이 된다).
이런실정이니, selector를 통해서 타 클래스에서도 숨겨놓은(.h에는 없는) 메소드 호출이 가능하기에...
애초에 프라이빗한 메소드는 없다.(프라이빗한 인스턴스 변수는 @private 등으로 선언할 수 있다.)


그렇다면, Objective C 에서는 접근지정은 할 수가 없는 것일까?!!

## 헤더파일

헤더파일 `.h`에는 해당 클래스의 명세가 나타난다.
가지고 있는 프로퍼티와 메소드들은 다른 클래스에서, 다른 사람들이 참조하기 쉽도록
`public` 퍼블릭 메소드 처럼 제공된다.

가장 간단하게는
`.h`파일에 메소드를 선언하지말고, `.m`파일에 정의만 하는 것이다.
그렇다면, 외부 사용자는 헤더파일을 참고할 때, 해당 메소드의 존재를 알지 못하니.
private 비슷한 기능을 낼 수 있다.

하지만, 여기는 조금의(거슬리는) 문제점이 있다.
컴파일러마져, private 메소드의 존재를 알지 못하기에 
~~에러~~는 아니지만 **경고**를 뱉어내고 만다.

이런 문제는 어떻게 해결할까?
역시 강력한 도구가 있다.
바로 `Category`

## Private Method with Category

바로 아래의 경우는 **@implementation**에만 정의를 한 경우이다.

```objectivec
// PrivateMethodTest.h
#import <UIKit/UIKit.h>

@interface PrivateMethodTest : UITableViewController {
   NSMutableArray *packages;
}

@end


// PrivateMethodTest.m
#import "PrivateMethodTest.h"

@implementation PrivateMethodTest

- (void)viewDidLoad {
   [super viewDidLoad];

   [self myPrivateMethod:packages];
}

- (void)myPrivateMethod:(NSMutableArray *)array {
}
@end
```

이 경우에서는 위ㅔ서 말한 것과 같이, 컴파일러의 에러를 마주할 수 있다.
(여기서 private한 메소드는 myPrivateMethod이다.)

---

이러한 코드를 다음과 같이 `(Private) 카테고리`를 써서 나타내었다.


```objectivec
// PrivateMethodTest.m
#import "PrivateMethodTest.h"

@interface PrivateMethodTest(Private)
- (void)myPrivateMethod:(NSMutableArray *)array;
@end


@implementation PrivateMethodTest

- (void)viewDidLoad {
   [super viewDidLoad];

   [self myPrivateMethod:packages];
}
@end

@implementation PrivateMethodTest (Private)
- (void)myPrivateMethod:(NSMutableArray *)array {
}
@end
```

이렇게 카테고리를 이용하여 Private Method를 구현할 수 있었다.
이러한 카테고리를 `익명`으로 사용하는 방법도 있다. 바로
`class continuation`이다.
즉, 카테고리의 이름을 비워놓는 것이다.
그렇게 되면, 따로 해당 인터페이스를 구현할 필요가 없고, 바로 해당
클래스 구현부에서 구현하면 된다.
바로 다음과 같이 말이다.

```objectiveC
// PrivateMethodTest.m
#import "PrivateMethodTest.h"

@interface PrivateMethodTest()
- (void)myPrivateMethod:(NSMutableArray *)array;
@end


@implementation PrivateMethodTest

- (void)viewDidLoad {
   [super viewDidLoad];

   [self myPrivateMethod:packages];
}


- (void)myPrivateMethod:(NSMutableArray *)array {

}

@end

```

여러모로,
숨은 매력이 많은 Objective C 이다.


