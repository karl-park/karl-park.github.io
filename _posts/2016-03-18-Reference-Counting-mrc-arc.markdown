---
layout: post
title:  "[Referece Counting] ARC vs MRC"
date:   2016-03-18 02:00:00
tag:
- iOS
- RefereceCounting
- ARC
- MRC
- Memory
ios: true
---

## ARC / MRC
> iOS 어플리케이션 개발을 위해서는 반드시 ARC나 MRC 중 한개를 골라서 진행해야 한다.

- Objective C 에서는 ARC와 MRC를 이용해서 메모리를 관리하고 있다. 
### Reference Counting
- 레퍼런스 카운팅은 메모리에 할당된 객체에 대하여 해당 객체를 참조중인 개수를 Counting 하여서 메모리를 관리하는 기법이다. 즉, `특정 객체에 몇개의 참조가 걸려있는지`를 카운팅하는 기법이라고 말할 수 있다.


### MRC (Manual Reference Counting)
- 지원 메소드( Objective-C : More Specifically Speaking, Cocoa Framework > Foundation Framework > NSObject )
	- alloc, new, copy, mutableCopy : 생성 + 소유권 획득
	- retain : 소유권 획득(참조중인 객체의 소유권 획득)
	- release : 소유권 포기
	- dealloc : 소멸

- 다음의 코드를 통해 대략적인 흐름을 파악해보자. 
	- 출처 : [GNUstep](http://gnustep.org)

```objective-c
    struct obj_layout {
        NSUInteger retained;
    };

    + (id)alloc
    {
        int size = sizeof(struct obj_layout) + size_of_object;
        struct obj_layout *p = (struct obj_layout *)calloc(1, size);
        return (id)(p + 1);
    }

    - (NSUInteger)retainCount
    {
        return NSExtraRefCount(self) + 1;
    }

    inline NSUInteger NSExtraRefCount(id object)
    {
        return ((struct obj_layout *)object)[-1].retained;
    }

    - (id)retain
    {
        NSIncrementExtraRefCount(self);
        return self;
    }

    inline void NSIncrementExtraRefCount(id object)
    {
        if ( ((struct obj_layout *)object)[-1].retained == UINT_MAX - 1 )
            [NSException raise:NSInternalInconsistencyException 
                format:@"NSIncrementExtraRefCount() asked to increment too far"];
           ((struct obj_layout *)object)[-1].retained++;
    }

    - (void)release
    {
        if ( NSDecrementExtraRefCountWasZero(self) )
            [self dealloc];
    }

    inline BOOL NSDecrementExtraRefCountWasZero(id object)
    {
        if ( ((struct obj_layout *)object)[-1].retained == 0 )
            return YES;
        else
        {
            ((struct obj_layout *)object)[-1].retained--;
            return NO;
        }
    }

    - (void)dealloc
    {
        NSDeallocateObject(self);
    }

    inline void NSDeallocateObject(id object)
    {
        struct obj_layout *o = &((struct obj_layout *)object)[-1];
        free(o);
    }
```

- 실제 Apple에서는 GNUstep의 경우와 같이 수명관리를 단순하게하는 것이 아니라, 레퍼런스 카운트 해쉬 테이블을 사용해서 레퍼런스 카우늩를 관리하고 있고, 그것을 이용해 각 메모리 블록에 접근할 수 있도록 구현하고 있다.

![](https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/referececounting/mrc.jpg)

### ARC (Automatic Reference Counting)
- ARC는 자바의 가비지 컬렉터와 비슷한 기능이라고 생각하면 된다. 컴파일 시에 retain, release, autorelease와 같은 메모리 관련 메소드를 자동적으로 추가해주기 때문이다.
- 지원 버전 : 
	- Mac OS X v.10.6, v10.7 이상
	- iOS 4 이상
	- XCode 4.2 이상
	- (Weak reference 의 경우는 iOS5 이상)
- 규칙 :
	- retain, release, retainCount, autorelease, dealloc 을 explicit하게 호출 할 수 없다. 마찬가지로, [super dealloc] 또한 호출 할 수 없다.
	- alloc 메소드를 통해서 생성한 객체의 해제는 런타임이 담당한다.
	- 기존의 NSAutorelease 객체를 사용할 수 없습니다. ARC를 사용하는 프로젝트에서는 @autoreleasepool 블록을 사용하여야한다.
	- Memory zone을 사용할 수 없다.
	- ARC를 사용하지 않는 이전 코드에서 함께 사용할 경우 new로 시작하는 속성명을 사용 할 수 없다.


### 결론
- 드는 생각인데, 물론 직접적인 메모리를 다루는 것은 힘이든다. retain 값을 일일이 염두에 두어가면서 개발을 해야하기 때문이다. 하지만, AOS, iOS와 같은 모바일 환경에서는 데스크탑 환경과는 다르게 그 메모리가 매우 제한되므로, 개발자의 직접적인 메모리 컨트롤이 필요할 경우가 잦다. 기존의 Legacy 코드들이 이러한 직접적인 메모리 컨트롤을 하는 경우가 많은 이유이기도 하다. 두 방법을 모두 염두를 해 두면서 개발하는 iOS 개발자가 되어야하겠다.