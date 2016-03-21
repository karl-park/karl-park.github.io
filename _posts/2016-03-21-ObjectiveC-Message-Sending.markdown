---
layout: post
title:  "[Objective-C] Message Sending"
date:   2016-03-21 00:00:00
tag:
- iOS
- Objective-C
- Message Sending
- Method Call
- SEL
- IMP
ios: true
---

## 메시지 전송
- Objective-C에서의 메소드 호출은, "`메시지를 전송`한다." 라고 표현한다.
```
[receiverObject message]
```

- 위와 같이 메소드를 호출하게 되는데, 이전의 Objective-C 런타임/구조 포스트에서 살펴본 것과 같이, receiverObject에 message(selector)를 보내게 되면, dispatch table 검색을 통하여(혹은 캐시) 메소드를 실행하게 된다.
-  만약 isa 포인터에 해당되는 클래스에 해당 메소드가 존재하지 않는다면, superclass를 재탐색하게 되며, Root Class 까지 탐색을 하게 된다.

- 위의 메시지 전달은 다음과 같이 컴파일 된다.

```
//파라미터가 없는 경우
objc_msgSend(receiverObject, selector)

//파라미터가 있는 경우
objc_msgSend(receiverObject, selector, arg1, arg2, ...)
```

---

- 이렇게 컴파일 된 메소드는 리시버(타겟) 객체에 메시지가 보내졌을 때, 다음과 같은 순서로 동작한다.

1. 무시되는 셀렉터인지 체크한다.(retain, release등은 garbage collection 이 활성화 되어있을 때에 무시된다.)
2. 타겟이 `nil`인지 체크한다.(이로인해서 Objective-C의 경우 `nil`에게 메시지를 보내더라도 아무 문제가 없다.)
3. 클래스 캐쉬에서 IMP를 발견한 경우, 해당 포인터를 따라 해당 함수로 jump
4. 클래스 캐쉬에 없다면, 클래스 디스패치 테이블을 검색하여 IMP를 찾고 해당 함수로 jump
5. 위의 두가지 경우에서 모두 IMP가 발견되지 않은 경우, 포워딩 매커니즘으로 jump !


## 셀렉터(SEL)와 구현(IMP)
- Objective-C의 클래스 구조와 메시지 전달 매커니즘을 보면, 조금은 신기한 것들이 많다. 가장 핵심적인 키워드는 바로 "분리"라는 키워드라고 생각한다.
- 그렇다면 무엇이 "분리"되어 있느냐 !!?
- 바로, `메소드`와 `셀렉터`, 그리고 `구현`이 분리되어 있다는 것이다.
- 즉, 메시지를 전달할 때는, receiverObject에게 `셀렉터`를 보내게 되고, 그 셀렉터에 맞는 메소드를 `dispatcherTable`에서 검색을 하게 된다. 그 후, 상응하는 `구현`을 다시 되돌려 줌으로써, receiverObject에게 코드를 실행하게 되는 것이다.
- 이렇게 특정 셀렉터와 특정 구현을 이어주는 것이 바로 `Method Structure(메소드 구조체)`이다. Method 구조체는 아래와 같다.

```
typedef struct objc_method {
SEL method_name;		// 문자열 포인터
char *method_types;		// 인수의 개수, 타입, 반환값 타입을 정의하는 문자열
IMP method_imp;			// 함수 포인터
} *Method;
```

- 여기서 `SEL`(문자열 포인터)은 셀렉터, `IMP`(함수 포인터)는 구현의 타입이다.

---

- 예를 들어보며, 이 포스트를 마치도록 하자.
![objective c message dispatch](https://raw.githubusercontent.com/MrKarl/MrKarl.github.io/master/assets/images/objectstructure/messageDispatch.png)
- 위 그림에서와 같이, 특정 객체(myInstance)에서는 getAuthor/flip이라는 셀렉터 메시지를 받게 되는데, 그러한 `SEL`들은 Method 구조체 상에서 상응하는 `IMP`를 찾게된다.