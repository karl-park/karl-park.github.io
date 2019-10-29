---
layout: post
title:  "[Kotlin] Hello Rx"
date:   2019-10-28 07:00:00
tags: kotlin reactivex reactiveprogramming functionalprogramming fp rxkotlin rxjava rx
categories: devstory
---

# "Hello Rx"
학습을 위해 Rx에 대해서 짧게 다뤄본다. Rx 사이트의 내용을 주로 참고하였다.




# Observable
- Observable : Observer 가 구독하는 대상
Observable이 emit(배출)하는 하나/연속된 항목에 Observer는 구독을 한다.

Observable(Observer Pattern)은 다음 2가지 방식으로 구현가능하다.
1. Pull
    - Observer가 매번 Pulling을 통해 Observable의 상태를 체크한다.
2. Push
    - Observable의 상태가 변경되었을 때, Observer에게 Push하여 알려준다.

> An Observer subscribes to an Observable.
> 
> An Observable emits items or sends notifications to its observers by calling the observers' methods.
> 

Observer는 때로는 **subscriber**, **watcher**, **reactor**라고 불리우기도 한다. 반면 Observable은 **producer**로 불리우기도 한다.



## onNext, onCompleted, onError
**subscribe()** 메소드로 **Observable**과 **Observer**를 연결 할 수 있다.

Observer의 주요 method는 다음과 같다.
- onNext
    - **emissions**
    - Observable이 아이템을 하나씩 배출 할 때마다, 실행된다. 2번 이상 실행 될 수도 있다.(0번 이상)
- onComplete
    - **notifications**
    - 에러가 없는 상황에서 onNext가 마지막으로 호출된 이후 호출된다.
- onError
    - **notifications**
    - 에러가 발생한 상황에 호출된다.


## 언제 emit을 할까?
Observable이 onNext로 아이템을 emission 하는 시기는 다음 2가지로 나뉜다.

**Hot Observables** 는 item이 생성되자마자 아이템을 emit 한다. 
그렇기 때문에, Observer가 늦게 붙게 되면, Observable이 생성한 아이템들의 중간부터 subscribe 될 수 있다.

**Cold Observables** 는 subscribe되는 Observer가 연결될 때 까지, emission을 하지 않는다. 
그래서, Cold Observable을 subscribe하는 Observer는 Observable이 emission하는 전체 아이템을 subscription 하는 것을 보장한다.

ReactiveX 구현체 중에는 **Connectable이라는 Observable**이 있다. 이 구현체는 subscription 여부와는 상관없이 connect() 메소드가 호출되기 전까지는 emission을 하지 않는다.


## Observable 연산자를 통한 Composition 

| Usage | Operation |
| - | - |
| [생성(Creating)](http://reactivex.io/documentation/operators.html#creating) | Create, Defer, Empty/Never/Throw, From, Interval, Just, Range, Repeat, Start, and Timer |
| [Observable 변환(Transforming)](http://reactivex.io/documentation/operators.html#transforming) | Buffer, FlatMap, GroupBy, Map, Scan, and Window |
| [Observable 필터링(Filtering)](http://reactivex.io/documentation/operators.html#filtering) | Debounce, Distinct, ElementAt, Filter, First, IgnoreElements, Last, Sample, Skip, SkipLast, Take, and TakeLast |
| [Observable 결합(Combining)](http://reactivex.io/documentation/operators.html#combining) | And/Then/When, CombineLatest, Join, Merge, StartWith, Switch, and Zip |
| [Operator 에러 핸들링](http://reactivex.io/documentation/operators.html#error) | Catch and Retry |
| [Utility](http://reactivex.io/documentation/operators.html#utility) | Delay, Do, Materialize/Dematerialize, ObserveOn, Serialize, Subscribe, SubscribeOn, TimeInterval, Timeout, Timestamp, and Using |
| [조건 및 불리언(Conditional and Boolean)](http://reactivex.io/documentation/operators.html#conditional) | All, Amb, Contains, DefaultIfEmpty, SequenceEqual, SkipUntil, SkipWhile, TakeUntil, and TakeWhile |
| [Mathmatical, Aggregate](http://reactivex.io/documentation/operators.html#mathematical) | Average, Concat, Count, Max, Min, Reduce, and Sum |
| [Observavle 변환(Converting)](http://reactivex.io/documentation/operators.html#conversion) | To |
| [Connectable Observable](http://reactivex.io/documentation/operators.html#connectable) | Connect, Publish, RefCount, and Replay |
| [Backpressure](http://reactivex.io/documentation/operators/backpressure.html) | 특정 제어흐름 원칙들을 적용하는 다양한 연산자들 |


대부분의 연산자들은 Operator Chanining이 가능하고, 그 순서 또한 중요하다.



## Reference
[Observable](http://reactivex.io/documentation/ko/observable.html)




# Operators
너무 많으므로 일단 생략. 
하나씩 살펴보려면, 아래 각 컴포넌트들이나 Observables 의 Composition 부분을 참고하자.

## Reference
[Operators](http://reactivex.io/documentation/ko/operators.html)



# Single
Single은 한 가지의 값(onNext, onCompleted 대신에 **onSuccess**) 또는 한 가지의 에러(**onError**)를 가지는 Observable의 한 형태이다.
(기본적으로 Observable은 0에서 무한대까지의 임의의 연속된 값들을 가진다.)

위의 메소드가 호출 되면, Single의 lifecycle은 끝이나고 subscription도 종료가 된다.

> Single은 JavaScript에서의 **Promise** 와 비슷한 것 같다. 단 한 개의 Item을 produce하거나 throw error를 하는 Promise와 거의 흡사하게 보인다.

## Reference
[Single](http://reactivex.io/documentation/ko/single.html)




# Subject
Subject는 **Observer과 Observable** 둘 다 사용될 수 있는 일종의 프록시이다.
**Observer** 이기 때문에 Observable를 한개 이상 구독할 수 있다.
**Observable** 이기도 하므로, 다시 reemit 할 수 있을 뿐만 아니라, 새로운 item을 emit 할 수도 있다.

Subject에는 다음 4가지 종류가 있다. (각각의 Rx 구현체(ex) RxKotlin, RxScala)마다 이름은 다소 상이 할 수 있다.)

- AsyncSubject
- BehaviorSubject
- PublishSubject
- ReplaySubject


## AsyncSubject
**AsyncSubject** 는 source Observable이 emit하는 가장 나중값을 emit 한다.
즉, Observable의 동작이 완료된 후에야 동작을 시작한다. 만약 source Observable이 아무런 값도 내뱉지 않으면, 값을 emit하지 않은채 complete 한다. error로 종료된다면, 에러를 그대로 notification 한다.


## BehaviorSubject
**BehaviorSubject** 는 Observer가 자기를 구독하기 시작하면, source Observable이 가장 최근에 emit한 item을 emit 한다. 만약, 아무 값도 emit 되지 않은 상태라면, 맨 처음값/기본값을 emit 한다.
그 이후, 계속해서 source Observable이 emit한 아이템들을 emit 한다.


## PublishSubject
**PublishSubject** 는 Observer가 구독을 시작한 이후의 item만 emit 한다.
즉, Hot Observable 이기 때문에 구독 전 배출된 아이템을 유실 할 수 있다.
모든 항목의 배출을 보장해야한다면, **Create**를 사용해서 명시적으로 Cold Observable 을 만들거나, 아래 설명하는 ReplaySubject를 사용하자.


## ReplaySubject
**ReplaySubject** 는 Observer가 구독한 시점과는 상관없이 emit된 모든 item을 emit 해준다. 
몇개의 overloaded 된 constructor를 제공하는데, 버퍼의 크기가 특정 이상으로 증가할 경우혹은 특정 시간이 경과된 경우 오래된 항목들을 지우는 것도 제공한다.
멀티 스레드 환경에서 ReplaySubject를 **Observer**로 사용 할 경우, 어느 항목을 먼저 Replay 해야하는지 모르는 모호함이 발생할 수 있으므로, onNext(or other on) 메소드들을 사용하지 말아야한다. 


## Reference
[Subject](http://reactivex.io/documentation/ko/subject.html)





# Scheduler
Observable Operator cascading(Chaining)에 Multithreading을 적용하려면, Scheduler를 사용하여 Operator Chaining(Or Observable)을 구현하면 된다.

일부 Observable Operator들은 사용할 Scheduler를 파라미터로 받는데, 자신의 연산 전부 혹은 일부를 전달된 Scheduler에서 실행시킨다.

즉, Observable과 Operator chain은 Scheduler 위에서 동작하고, **Subscribe()** 메소드가 호출되는 쓰레드와 동일한 곳에서 실행되어 Observer에게 emit 을 한다.

**SubscribeOn** Operator는 다른 Scheduler를 지정해서 Observable이 처리해야할 Operator들을 실행시킨다.
**ObserveOn** Operator는 Observable이 Observer에게 emit을 보낼 때 사용 할 Scheduler를 명시한다.

즉, 아래 Marble Diagram 에서처럼 **subscribeOn** observeOn
<img src="http://reactivex.io/documentation/operators/images/schedulers.png" width=600/>


## SubscribeOn
- subscribeOn은 Observable 객체가 실행될 쓰레드를 정한다.
- 예를 들면 
```kotlin
userApi.getUsers().subscribeOn(newThread())
```
으로 사용했다면 getUsers() 가 newThread 안에서 실행됨.

## ObserveOn
- observeOn은 연쇄되는 연산이 실행될 쓰레드를 정한다.

- 예를 들면 
```kotlin
userApi.getUsers().subscribeOn(newThread()).observeOn(mainThread()).subscribe({
    Log.d("Log", "Logging")
},{},{}) 
```
으로 사용했다면 getUsers() 가 newThread 안에서 실행되고, subscribe() 코드가 메인 쓰레드 안에서 실행된다.




```kotlin
myObservable // observable will be subscribed on i/o thread
      .subscribeOn(Schedulers.io())
      .observeOn(AndroidSchedulers.mainThread())
      .map { /* this will be called on main thread... */ }
      .doOnNext{ /* ...and everything below until next observeOn */ }
      .observeOn(Schedulers.io())
      .subscribe { /* this will be called on i/o thread */ }
```





# Flowable
**Flowable** 은 RxJava2 에 등장한 개념이다. 
RxJava1에서 고심하였던 여러 문제들을 해결한 버전이 Version2인데, 그 중 정말 중요한 토픽이 바로, **Backpressure**이다.

## Backpressure
**Backpressure** 는 observable이 observer(consumer)가 그것을 handling하는 것보다 더 빠르게 item을 emitting 하는 개념이다.

만약 observable이 observer가 consuming 하기도 전에 계속 emitting을 한다면, buffer overflow나 out of memory 이슈등이 생길 수 있을 것이다. Flowable 은 이러한 것을 고려하여 **BackPressureStrategy** 를 설정하여, 어떻게 Backpressure 상황을 핸들링할지를 결정한다.

Backpressure Strategy는 다음이 있다.
- BUFFER : RxJava1에서 처럼 item들을 다루지만, Buffer size를 지정 할 수 있다.
- DROP : Consumer가 handling하지 못하는 item 들을 버린다.
- ERROR : Downstream이 emitted item을 따라잡지 못하면 에러를 던진다.
- LATEST : 마지막 emitted item만을 keep 하며, 이전 값을 덮어쓴다.
- MISSING : onNext 이벤트에서 buffering과 dropping을 하지 않는다.



# Maybe
**Maybe** 는 **Single**과 거의 흡사하다. 위에서 설명하였듯, Single은 단 한개의 emitted item과 error를 반환한다. Maybe는 Single과 거의 흡사하지만, 다른 점은 **"아무 것도 emit 하지 않는 것을 허용한다"** 는 것이다.




# Completable
**Completable** 는 말 그대로, completion 에만 관심이 있다. 그래서, onNext, onSuccess 등과 같은 콜백이 없고, **onComplete(), onError() 메소드만 존재** 한다.



## Reference
- [Scheduler](http://reactivex.io/documentation/ko/scheduler.html)
- [RxAndroid and RxKotlin](https://www.raywenderlich.com/2071847-reactive-programming-with-rxandroid-in-kotlin-an-introduction#toc-anchor-001)