---
layout: post
title:  "[Android] Naver Tech Concert 2018 (Day2) - Part3"
date:   2018-11-12 09:00:00
tags: android seminar naver navertechconcert techconcert java kotlin
categories: devstory
---


##### 다른 세션들
[Naver Tech Concert 2018 (Day2) - Part1](/devstory/2018/11/12/Naver-Tech-Concert-Part1/)
[Naver Tech Concert 2018 (Day2) - Part2](/devstory/2018/11/12/Naver-Tech-Concert-Part2/)
[Naver Tech Concert 2018 (Day2) - Part3](/devstory/2018/11/12/Naver-Tech-Concert-Part3/)



# 5. 안드로이드에서 코루틴은 어떻게 적용할 수 있을까?
부제 : 코루틴 적용 및 ReactiveX(RxJava/RxKotlin)와 비교한다면?

이번 시간에는 요기요에서 안드로이드 개발을 담당하고 있는 권태환님의 "안드로이드 코루틴" 발표를 요약정리하였습니다.

참고로, 코루틴은 Kotlin에 이제 막 도입된 개념으로, JavaScript의 Generator, 유니티의 코루틴과 그 뿌리가 같습니다.

- guide: https://kotlinlang.org/docs/reference/coroutines-overview.html
- google codelab : https://codelabs.developers.google.com/codelabs/kotlin-coroutines/#0


## Kotlin Coroutines


코루틴이란 무엇일까요? 코루틴을 알기전에 "서브루틴"이라는 개념부터 짚고 넘어가봅시다.

Subroutine은 우리가 자주 쓰이는 function/method 입니다. 즉 **return**이 호출되기 전까지는 다음 구문을 실행하지 않는 구문을 의미합니다.
(subroutines : a procedure, a function, routine, a method or a subprogram)
, ...

![1.png](/static/assets/img/posts/navertachconcert18/3-1.png)
![1.png](/static/assets/img/posts/navertachconcert18/3-2.png)


Subroutine을 알았다면 Coroutine은 서브루틴을 이용해서 다음과 같이 설명할 수 있습니다. (Subroutine은 Coroutine의 한 종류라고도 볼 수 있습니다. 혹은 Subroutine을 일반화하면 Coroutine이 되는 것이죠)

> Coroutine
> **Entry Point 여러개** 를 허용하는 Subroutine, 언제든 **일시정지** 하고 다시 실행가능. Event Loop, Iterators, Infinite List, Pipe 등을 구현하는데 적합함. (위키피디아)
> <br/> <br/>
> 원문
> Coroutines are computer-program components that generalize subroutines for non-preemptive multitasking, by allowing multiple entry points for suspending and resuming execution at certain locations. Coroutines are well-suited for implementing familiar program components such as cooperative tasks, exceptions, event loops, iterators, infinite lists and pipes.


## RxJava vs. Coroutine
발표내용은 Android의 onClick 이벤트를 Rx와 Coroutine을 이용하여 각각 구현해보고, 그 차이점을 알리는 것에 집중하였습니다. 전체 코드내용을 싣기는 힘드므로, 다른 블로그 글로 해당 내용은 정리하도록 하겠습니다. 여기서는 단순히 Rx와 Coroutine의 차이점을 짚어보는 것으로 마무리하겠습니다.

| RxJava | Coroutine |
| -------- | ------------- |
| Observable, Streams | 함수 형태 |
| 기존 Thread 보다는 간단한 코드 처리 | light-weight threads |
| Streams을 통해 데이터 처리 용이 | 모든 Routine 동작을 개발자가 처리 가능 |
| Thread간 교체가 간단 | 아직은 필요한 라이브러리를 구현해서 사용해야함 (RxView와 같은 라이브러리 개발 필요) |
| RxJava를 활용한 수 많은 라이브러리 활용 가능 | |
| 러닝커브가 높음 (용어를 모르면 코드 활용 이유를 알 수 없음 | |
| 처음 학습 비용이 높음 | 처음 학습 비용이 낮음 |
| 많은 라이브러리를 활용 가능(많은 예제, 문서화) | 아직 부족한 라이브러리(직접 만들 수 있음. 문서화는 잘된편) |




## Reference
- http://dogfeet.github.io/articles/2012/coroutine.html
- https://medium.com/til-kotlin-ko/kotlin의-coroutine은-어떻게-동작하는가-789291da6a50
- https://thdev.tech/kotlin/2018/10/04/Kotlin-Coroutines/
- https://thdev.tech/kotlin/2018/10/11/Kotlin-Coroutines-Android/




# 6. 자동화, 계륵에 살 붙이기
부제 : Evolution of Android Automation Test

Naver Tech Concert 2번째 날의 마지막 세션으로 "자동화, 계륵에 살 붙이기"라는 주제로 NTS에서 소프트웨어 품질 보증을 담당하고 있는 송의초경님의 발표가 있었습니다.

해당 세션에서는 "개발자" 관점의 QA라기 보다는 "NTS"에서 품질보증을 위해 직접 만든 소프트웨어를 살펴보고, QA가 바라보는 소프트웨어 품질향상 지표들에 대해서 살펴보았습니다.


## UI Automation Test
테스트 동작 중 화면상의 마우스 동작, 키보드 입력 등을 자동화 할 수 있는 Script 형태로 작성하여, 이후 동일한 형태로 Replay 함으로써 Regression Test를 지원하는 테스트 자동화 방식을 ***UI Automation Test*** 라고 합니다. 

개발자들은 각 모듈간의 Unit 테스트에 집중하고, QA는 이러한 모듈간의 "시나리오"에 따른 테스트 및 UI 테스트에 집중하는 것으로 현재 업무중입니다.


## Appium
NTS에서는 UI Automation Test를 위해 [appium (Apache License 2.0)](http://appium.io/)을 도입하여 사용중입니다. 

Appium을 선택하기전 고려한 Frameworks들은 다음과 같고 최종적으로 Appium을 선택하였습니다.
![3.png](/static/assets/img/posts/navertachconcert18/3-3.png)


## Daily Jenkins Build
NTS에서는 매일 아침(혹은 정기적으로) Jenkins Build를 통해 그 날의 빌드를 자동화 테스트하고 있습니다. 단순 UI Automation 테스트를 통해 클라이언트의 결함을 테스트할 뿐만 아니라, 매일 테스트를 통해 "서버 상태"등도 모니터링 하는 데 사용중입니다.