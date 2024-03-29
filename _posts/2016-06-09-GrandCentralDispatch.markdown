---
layout: post
title:  "[Objective-C] Grand Central Dispatch 02"
date:   2016-06-10 03:00:00
tags: objc ios GCD GrandCentralDispatch
categories: devstory
---
이전 [Grand Central Dispatch 01](/devstory/2016/05/24/GCD/) 글을 바탕으로, TOAST Meetup에 글을 기고했는데요, 그대로 옮겨놓습니다.

- 원글 링크 : [TOAST Meetup: GCD](http://meetup.toast.com/posts/88)


# Objective C 에서의 GCD
### Overview

![image](http://image.toast.com/aaaadh/alpha/2016/techblog/001%281%29.png)

안녕하세요. NHN Etertainment 플랫폼SDK개발팀 박판기입니다.
신입으로 입사를 한지 얼마 되지않아서, 아직 대학 때의 기억이 비교적 생생한데요.
그 중에서도 멀티프로세싱, 쓰레드에 대한 기억은 여전히 막막함으로 다가옵니다.

이번 시간에는 Objective C 에서의 멀티코어 프로세싱 기술인 GCD와, GCD의 기반인 Block Coding, 그리고 이들을 있게한 LLVM에 대해서 살펴보겠습니다.


### 바야흐로 LLVM의 시대
2016년 현재, 지금은 위의 소제목처럼 LLVM의 시대라고 해도 과언이 아닙니다.
Linux, Windows, Mac OSX, 쟁쟁한 OS 들이 모두 LLVM을 선택하여 사용하고 있기 때문입니다. 그렇다면, 이 쟁쟁한 위상의 LLVM은 무엇일까요?

LLVM은 Low Level Virtual Machine의 약자로써, 쉽게 말하자면 컴파일러입니다. 컴퓨터를 처음 배우는 학부생들이 많이 다뤘던 컴파일러는 주로 gcc 일텐데요, 이 gcc를 대체하고 있는 컴파일러계의 강자가 바로 LLVM입니다.

![image](http://image.toast.com/aaaadh/alpha/2016/techblog/002%281%29.png)

(그림1. LLVM 구조)

LLVM은 gcc가 가지고 있는 한계들을 극복한, C++기반의 새로운 컴파일러를 만들자는 프로젝트로 부터 시작되었습니다. 이러한 프로젝트를 애플은 적극 지원하고 있구요.

Apple은  Xcode3 이전까지는 gcc를 이용해왔습니다. 이후 부터는 LLVM으로 옮기면서, 많은 성능 및 기능 향상을 만들었는데요, 그것들은 다음과 같습니다.

- 컴파일 속도의 향상
- 실행파일 크기 감소
- 실행 속도 증가
- 이번 포스트의 핵심 주제인 GCD, Block
- 편리해진 Xcode의 편집기능, 실시간 에러
![image](http://image.toast.com/aaaadh/alpha/2016/techblog/003.png)

(그림2. GCD로 인해 편리해진 Xcode)

다음 절에서는 LLVM을 기반으로, 태어난 우리의 Hero. GCD에 대해 알아보도록 합시다.


#### GCD (Grand Central Dispatcher)
GCD는 처음에 소개했다시피, 멀티코어 프로세서를 위한 Thread 프로그래밍 방법(C Library)입니다. 기존의 스레드 관리 기법은 개발자가 직접 락을 걸고, 쓰레드풀을 관리하는 등의 수고가 필요했는데요, GCD에서는 Thread를 OS가 자동 관리 및 분배를 해줍니다.

이전에는 쓰레드를 직접 생성하고, 관리하는 등의 일을 개발자가 직접하였는데요, GCD를 통해서는 Dispatch Queue에 Task를 생성해서 집어넣기만 하면 됩니다 !

##### Dispatch Queue
Dispatch Queue는 Task를 적재하는 데이터 구조입니다. 데이터 구조의 Queue이므로, 작동 방식이 Serial(순차적)이든 Concurrent(동시)이든, 언제나 FIFO(First In First Out)방식으로 동작합니다.

- Serial Dispatch Queue : Queue에 Push된 순서대로 1개의 Task씩 실행하며, 해당 Task가 끝나기를 기다립니다. Queue를 여러개 만들 수도 있으며, 각 Queue들은 Concurrency하게 돌아갑니다.
- Concurrent Dispatch Queue : Global Dispatch Queue라고도 불리우며, 여러개의 task를 Concurrent하게 실행합니다. 실행순서는 Push한 순서대로 실행되며, 동시에 실행되는 Task는 시스템의 환경에 따라 달라집니다.
- Main Dispatch Queue : Application의 Main Thread에서 Serial하게 실행되는 Task들을 관리하는 Queue입니다. 해당 Queue는 Application의 Run loop에서 작동하게 됩니다.

다음으로는 이러한 GCD를 사용하는데 필요한 Block Coding에 대해서 살펴보겠습니다.


#### Block Coding
Block은 다른 언어의 Lambda나 Closure의 개념과 흡사한 부분이 많습니다. 그렇기에, 해당 스코프들에 대해서 변수/환경을 capturing 할 수도 있습니다.

##### Block Syntax

---
블록은 다음과 같이 만들 수 있습니다.

```objc
^{
    NSLog(@"This is a Block");
}
```

위와 같은 블록을 변수에 대입할 수도 있습니다.
```objc
void (^completion)(void) = ^{
    NSLog(@"This is a Block");
}

// invoke
completion();
```

---

매개변수를 받거나, 값을 반환할 수도 있습니다.
```objc
NSString* (^sayHelloBlock)(NSString*, NSString*) = ^(NSString *name, NSString *country) {
    NSString *helloStr = nil;
    if (country caseInsensitiveCompare:@"kr"] == NSOrderedSame) {
        helloStr = [NSString stringWithFormat:@"%@, 만나서 반갑습니다.", name];
    } else if (country caseInsensitiveCompare:@"en"] == NSOrderedSame) {
        helloStr = [NSString stringWithFormat:@"%@, Nice to meet you.", name];
    } else {
        helloStr = [NSString stringWithFormat:@"%@, Where are you from?", name];
    }
    
    return helloStr;
}


NSLog(@"%@", sayHelloBlock(@"Panki", @"KR"));
```

---


다음과 같이 같은 Lexical Scope(Enclosing Scope)에 있는 변수들을 Capturing 할 수도 있습니다.

```objc
int testA = 89;
 
void (^testBlock)(void) = ^{
    NSLog(@"testA is: %d", testA);
};

testA = 4;

testBlock();

///// OUTPUT
> testA is: 89
```

---

\_\_block 키워드를 써서, Capturng 한 변수를 수정하거나 변경사항을 공유할 수도 있습니다.
```objc
__block int testA = 89;
__block int testB = 91;

void (^testBlock)(void) = ^{
    NSLog(@"testA is: %d", testA);
    NSLog(@"testB is: %d", testB);    
    testB = 77;
    
};

testA = 4;
testBlock();
NSLog(@"Value of original A is now: %i", testA);
NSLog(@"Value of original A is now: %i", testB);

///// OUTPUT
> testA is: 89
> testA is: 91
> Value of original A is now: 4
> Value of original B is now: 77
```

---

이러한 Block을 메소드의 파라미터로 넘길수도 있습니다.
```objc
- (void)doSomethingWithCompletion:(void (^)(int, int)) completion {
    // Do Sth.
    ...
    
    completion(28, 89);
}



// 외부에서의 call
[self doSomethingWithCompletion:(void(^)(int a, int b)) {
    int c = a+b;
    // Do Anything
}];
```
어디서 많이 본듯한 것이, 마치 CallBack과 같죠?

---

Block에 대한 설명은 이쯤하고, 본격적으로 Block을 사용하여 GCD를 사용하는 법을 알아봅시다.

### GCD Usage

#### 1. Dispatch Queue 얻거나 생성하거나

```objc
// 1) Dispatch Queue를 생성합니다.
// 1-1) serial dispatch queue를 생성합니다.
dispatch_queue_t serialQueue = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);
// 1-2) concurrent dispatch queue를 생성합니다.
dispatch_queue_t concurrentQueue = dispatch_queue_create("test", DISPATCH_QUEUE_CONCURRENT);

// 2) Main Dispatch Queue를 얻어옵니다.
dispatch_queue_t mainQueue = dispatch_get_main_queue();

// 3) Global Dispatch Queue를 얻어옵니다.
dispatch_queue_t globalQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```


#### 2. Dispatch Queue에 Task 추가
Task를 추가할 때, 2가지의 방법으로 추가를 할 수 있습니다.
1. Sync하게 Task추가
2. Async하게 Task추가

Dispatch Queue가 Serial/Concurrent의 2가지(Main은 serial, Global은 concurrent하게 동작)이므로, Task 추가 방법에 따라서 다음 4가지 경우의 수가 나오게 됩니다.
1. Seral Dispatch Queue에 Sync하게 Task 추가
2. Seral Dispatch Queue에 Async하게 Task 추가
3. Concurrent Dispatch Queue에 Sync하게 Task 추가
4. Concurrent Dispatch Queue에 Async하게 Task 추가



예제를 보면서 각 상황에 대해서 설명하겠습니다.

-1. Serial Dispatch Queue에 Sync하게 Task 추가
```objc
dispatch_queue_t queue = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);
dispatch_sync(queue, ^{ NSLog(@"1"); });
dispatch_sync(queue, ^{ NSLog(@"2"); });
dispatch_sync(queue, ^{ NSLog(@"3"); });
```

`Serial Queue`를 생성한 후, 위와 같이 `Sync` 하게 Task를 추가하게 되면,
넣은 순서대로 실행이 되므로, 결과는 아래와 같습니다.
> 1
> 2
> 3

---

-2. Serial Dispatch Queue에 Async하게 Task 추가
```
dispatch_queue_t queue = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);
dispatch_async(queue, ^{ NSLog(@"1"); });
dispatch_async(queue, ^{ NSLog(@"2"); });
dispatch_async(queue, ^{ NSLog(@"3"); });
```

`Serial Queue`를 생성한 후, 위와 같이 `Async` 하게 Task를 추가하게 되어도, 결국은 Serial하게 1개의 Task씩 실행하며, 다른 Task의 추가를 먼저 들어간 Task의 실행까지 Blocking 하므로, 위와 같이 동일한 실행순서를 보여줍니다.
> 1
> 2
> 3

---

-3. Concurrent Dispatch Queue에 Sync하게 Task 추가
```
dispatch_queue_t queue = dispatch_queue_create("test", DISPATCH_QUEUE_CONCURRENT);
dispatch_sync(queue, ^{ NSLog(@"1"); });
dispatch_sync(queue, ^{ NSLog(@"2"); });
dispatch_sync(queue, ^{ NSLog(@"3"); });
```
위와 같이 `Concurrent Queue`를 생성한 후, Sync하게 Task를 추가하게되면, 각 Task가 실행되기 전까지 Task추가를 하지않으므로, 아래와 같은 결과를 보여줍니다.
> 1
> 2
> 3

---

-4. Concurrent Dispatch Queue에 Async하게 Task 추가
```
dispatch_queue_t queue = dispatch_queue_create("test", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(queue, ^{ NSLog(@"1"); });
dispatch_async(queue, ^{ NSLog(@"2"); });
dispatch_async(queue, ^{ NSLog(@"3"); });
```
`Concurrent Queue`에 `Async`하게 Task를 추가하게 되면, 각 Task가 Queue에 들어가게되며, 특정한 개수만큼 동시에 실행이 되게 됩니다. 그래서 아래와 같은 결과(상황에 따라 계속 달라지는)를 얻게 됩니다.
>1
>3
>2

---

#### 3. Dispatch Queue에 Timer 추가
Dispatch Queue에 특정 시간 이후에 Task를 추가하는 방법에 대해서 설명하겠습니다.

특정 시간 후, Dispatch Queue에 Task를 추가하기 위해서는 `disaptch_after`를 사용합니다.
사용 예는 다음과 같습니다.

```objc
dispatch_queue_t queue = dispatch_queue_create("test",  DISPATCH_QUEUE_SERIAL);

double delayAfter = 3.0;
dispatch_time_t pushTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayAfter * NSEC_PER_SEC));

dispatch_after(pushTime, queue, ^{ NSLog(@"1"); });
dispatch_async(queue, ^{ NSLog(@"2"); });
dispatch_async(queue, ^{ NSLog(@"3"); });
```

위와 같은 구문을 실행하게 되면, 3초 후에 Queue에 추가되어 실행이 되게 되므로 실행결과는 다음과 같습니다.
> 2
> 3
> 1

* Dispatch Queue에 Task를 추가한다는 의미는 `특정 시간 이후에 실행된다`는 의미와 같지 않습니다. Task 추가 시간과, 실행시간은 엄연히 구분해서 이해해야합니다.

---

#### 4. Dispatch Queue의 우선순위
Global Queue나 Main Queue를 제외한, 사용자 생성큐(dispatch_queue_create(...))의 우선순위는 Global Queue의 Default Queue와 그 우선순위가 같습니다. 이러한 우선순위를 바꾸기 위해서 우리는 `dispatch_set_target_queue`를 사용할 수 있습니다.

```objc
dispatch_queue_t queueHigh = dispatch_queue_create("test1", DISPATCH_QUEUE_SERIAL);
dispatch_queue_t queueLow = dispatch_queue_create("test2", DISPATCH_QUEUE_SERIAL);

dispatch_queue_t globalQueueHigh = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);
dispatch_set_target_queue(queueHigh, globalQueueHigh);

dispatch_queue_t globalQueueLow = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_LOW, 0);
dispatch_set_target_queue(queueLow, globalQueueLow);

dispatch_async(queueLow, ^{ NSLog(@"1"); });
dispatch_async(queueHigh, ^{ NSLog(@"2"); });
dispatch_async(queueLow, ^{ NSLog(@"3"); });
dispatch_async(queueHigh, ^{ NSLog(@"4"); });
```

위와 같은 구문을 실행시키면, queueHigh의 우선순위가 높게 배정되므로 다음과 같은 결과를 얻을 수 있습니다.
> 2
> 4
> 1
> 3

---

#### 5. Dispatch Group
만약에, 일련의 Tasks 들을 같은 그룹으로 묶어서 처리하고 싶을 때는 어떻게 처리하여야할까요? 그리고 그 일련의 Tasks들이 작업을 모두 수행했을 때, 어떻게 Noti를 받을 수 있을까요. 이를 위해 다음과 같은 기능을 제공합니다. `dispatch_group_create, dispatch_group_async, dispatch_group_notify`

```objc
dispatch_group_t group = dispatch_group_create();
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

dispatch_group_async(group, queue, ^{ NSLog(@"1"); });
dispatch_group_async(group, queue, ^{ NSLog(@"2"); });
dispatch_async(queue, ^{ NSLog(@"3"); });
dispatch_group_notify(group, queue, ^{ NSLog(@"작업이 모두 끝났습니다."); });
dispatch_async(queue, ^{ NSLog(@"4"); });
dispatch_async(queue, ^{ NSLog(@"5"); });
```

위의 작업을 수행하면 결과는 다음과 같습니다. (상황에 따라 달라질 수 있으나, 한 그룹안의 작업이 끝난 후, notify되는 순서는 보장됩니다.)

> 2
> 1
> 3
> 5
> 작업이 모두 끝났습니다.
> 4

#### 6. Dispatch Queue를 이용한 싱글톤 생성
개발을 하다보면, 싱글톤을 자주 접하게 되고, 구현도 하게 됩니다. GCD 에서는 싱글톤을 생성하기 위한 방법도 제공하는데요, `disipatch_once`가 바로 그것입니다.

```objc
- (MyCustomClass *)sharedInstance {
    static dispatch_once_t onceToken;
    static MyCustomClass *instance = nil;

    dispatch_once(&onceToken, ^{
        instance = [[MyCustomClass alloc] init];
    });

    return instance;
}
```


#### 맺으며
GCD 를 사용하는 여러 방법을 살펴보았습니다. Dispatch Queue를 생성하고, Queue에 Task들을 Async 혹은 Sync하게 Push하는 법과, 일정 시간 후 Queue에 Task를 추가하는 법, 싱글톤을 생성하는 법 등을 다뤘습니다.

해당 포스트에서 다루지 못한 여러 GCD 내용들도 있습니다. 가령, `dispatch_apply`나 `dispatch_barrier_async, dispatch_barrier_sync`, 세마포어를 다루는 `dispatch_semaphore_t` 등에 대한 것은 기회가 되면 다음 포스트에서 다뤄보도록 하겠습니다.

Dispatch Queue를 사용하는 것만이 정답은 아닙니다. 직접적으로 Thread를 관리해야 할 경우도 있고, NSOperationQueue를 사용하는 방법도 있습니다. 하지만, 코드의 간결성과 직관적인 것. 그리고 무엇보다 Apple에서 Thread를 직접 핸들링하지 말라고 권고하는 등의 이유로 GCD의 사용을 권합니다.

여기까지 Objective C에서의 GCD에 대해서 알아봤습니다. 조금이나마 도움이 되셨기를 바라며 더욱 유익한 내용으로 다시 뵙기를 기대합니다.
감사합니다.


#### Reference
- Apple 문서
    - Block : [https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)
    - GCD : [https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/)
    - LLVM :  [https://developer.apple.com/library/watchos/documentation/CompilerTools/Conceptual/LLVMCompilerOverview/index.html](https://developer.apple.com/library/watchos/documentation/CompilerTools/Conceptual/LLVMCompilerOverview/index.html)
- Blogs
    - LLVM : [http://kyejusung.com/2015/11/llvm%EC%9D%B4%EB%9E%80-clang-%EB%B9%84%ED%8A%B8%EC%BD%94%EB%93%9C-%ED%8F%AC%ED%95%A8/](http://kyejusung.com/2015/11/llvm%EC%9D%B4%EB%9E%80-clang-%EB%B9%84%ED%8A%B8%EC%BD%94%EB%93%9C-%ED%8F%AC%ED%95%A8/)
    - GCD : [http://padgom.tistory.com/entry/iOS-%EA%B8%B0%EB%B3%B8-GCDGrand-Central-Dispatch-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0](http://padgom.tistory.com/entry/iOS-%EA%B8%B0%EB%B3%B8-GCDGrand-Central-Dispatch-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)