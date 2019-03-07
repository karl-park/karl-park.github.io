---
layout: post
title:  "[Java] Integer Cache"
date:   2019-03-05 09:00:00
tags: java cache integercache integer byte character short long
categories: devstory
---

안녕하세요. 이번시간에는 Naresh Joshi의 [[Java Integer Cache — Why Integer.valueOf\(127\) == Integer.valueOf\(127\) Is True]](https://medium.com/@njnareshjoshi/java-integer-cache-why-integer-valueof-127-integer-valueof-127-is-true-e5076824a3d5) 블로그 글을 옮겨보며, Java Integer Cache에 대해서 짧게 살펴보는 시간을 가져보겠습니다.


- - -

인터뷰에서, 내 친구가 다음과 같은 질문을 받았습니다.
```java
Integer a = 127; 
Integer b = 127;
```
위와 같은 2개의 Integer 객체가 있습니다.

```java
a == b (true? false?)
```
**a == b** 의 답은 뭘까요?

---

본문에서는 이 질문에 대한 답과, 답에 대한 설명을 말하고자 합니다.
답을 간단히 말하자면, `int` 리터럴을 `Integer` reference로 직접 대입하는 것은 `auth-boxing` 컨셉의 예입니다. 리터럴 값이 객체로 변환되는 코드는 컴파일러에 의해 수행되고, 컴파일 시간동안, 컴파일러는 `Integer a = 127;`을 `Integer a = Integer.valueOf(127);`로 변경합니다.


Integer 클래스는 내부에서 integer 사용을 위해 `IntegerCache`를 관리합니다. 이 캐시의 기본 범위는 `-128 ~ 127`이며, `Integer.valueOf()` 메소드는 캐시 범위에 해당하는 objects를 리턴합니다. 그렇기에, `a`와 `b` 둘다, 같은 object 를 가리키게 되므로, `a == b`가 `true`가 됩니다.

- - -

위의 간단한 답을 조금 더 이해하기 위해, 2가지 카테고리로 분류되는 모든 Java 타입들을 살펴봅시다.

1. **Primitive Types** : 자바에는 8개의 primitive types(byte, short, int, long, float, double, char, boolean)이 있고, 이들은 binary bits 형식으로 그들 **값을 직접** 가지고 있습니다.
<br/>
2. **Reference Types** : Classes, Interfaces, Enums, Arrays 등과 같은 primitive types가 아닌 모든 타입들은 Reference Type입니다. Reference Type은 "object" 그 자신을 가지는 것이 아니라, **"object address"** 를 가지고 있습니다. 
  예를 들어서, `Integer a = new Integer(5); Integer b = new Integer(5);` 가 있을 때, a와 b는 binary 값인 `5`를 가지고 있는 것이 아니라, 각각 `5`의 값을 가지고 있는 objects의 메모리 주소값을 가지고 있습니다. 
  그래서, `a`와 `b`를 `a == b`로 비교를 할 때는 사실 두 개의 메모리 주소를 비교하는 것이고, `false`를 얻어야합니다. 실제 `a`와 `b`의 동치 비교(equality)를 할 때는 `a.equals(b)`로 비교를 해야합니다.
  
- Reference types은 다음 4개의 카테고리로 세분화 할 수 있습니다.
    - [Strong, Soft, Weak, Phantom References](https://www.programmingmitra.com/2016/05/types-of-references-in-javastrong-soft.html)


---

Java는 모든 primitive types에 대해서 **wrapper classes**를 제공하고있고, **auth-boxing, auto-unboxing**을 지원합니다.


```java
// auto-boxing의 예, c 는 reference type
Integer c = 128; // 컴파일러가 다음과 같이 변환합니다 : Integer c = Integer.valueOf(128);   

// auto-unboxing의 예, a는 primitive type 
int e = c; //  컴파일러가 다음과 같이 변환합니다 : int e = c.intValue();
```

만약 `a`와 `b` interger 객체를 만들고, 비교 연산자 `==` 로 그들을 비교하면, `false` 값을 얻을 수 있습니다. 두 레퍼런스는 각각 다른 object를 들고 있기 때문이지요.

```java
Integer a = 128; // 컴파일러가 다음과 같이 변환합니다 : Integer a = Integer.valueOf(128);
Integer b = 128; // 컴파일러가 다음과 같이 변환합니다 : Integer b = Integer.valueOf(128);

System.out.println(a == b); // false
```

---

`하지만` 만약 우리가 `127`이라는 값을 대입하고, `==` 로 비교한다면, `true` 값을 얻습니다. 왜 그럴까요?
```java
Integer a = 127; // 컴파일러가 다음과 같이 변환합니다 : Integer a = Integer.valueOf(127);

Integer b = 127; // 컴파일러가 다음과 같이 변환합니다 : Integer b = Integer.valueOf(127);

System.out.println(a == b); // true
```

위 코드에서 보는 것과 같이, `a`와 `b`에 서로 다른 object를 대입하였음에도, `a == b`가 `true`를 리턴한다는 것은 `a`와 `b`가 동일한 object를 가리키고 있다는 것을 의미합니다.

어떻게 위의 비교문이 true를 리턴할 수 있을까요? 무슨 일이 일어났을까요? `a`와 `b`가 같은 object를 가리키고 있을까요?
위에서 우리는, `Integer a =127;` 구문이 **auto-boxing**의 예이고, 컴파일러가 자동으로 이 라인을 `Integer a = Integer.valueOf(127);`로 바꿔준다는 것을 알고있습니다.

이런 integer object들을 반환하는 `Integer.valueOf()` 메소드가 무엇인가를 하고 있다는 것을 알 수 있습니다.
**Integer.valueOf()** 소스코드를 살펴보면, 우리는 다음과 같은 사실을 알 수 있습니다.

우리가 `IntegerCache.low` 보다 크고 `IntegerCache.high` 보다 작은 int 리터럴 `i`를 넘긴다면, **Integer.valueOf()** 메소드는 `IntegerCache` 로부터 Integer 객체들을 리턴합니다.
**IntegerCache.low** 와 **IntegerCache.high** 의 기본값은 각각 `-128`과 `127`입니다.

즉, **Integer.valueOf()** 메소드는 **-128** 에서 **127** 사이의 int literal을 넘겨줄 때, 새로운 Integer 객체를 생성하고 리턴하는 대신,  내부의 **IntegerCache** 객체에서 Integer 객체를 반환한다는 것입니다.

```java
/** 
* Returns an {@code Integer} instance representing the specified 
* {@code int} value. If a new {@code Integer} instance is not 
* required, this method should generally be used in preference to 
* the constructor {@link #Integer(int)}, as this method is likely 
* to yield significantly better space and time performance by 
* caching frequently requested values. 
* 
* This method will always cache values in the range -128 to 127, 
* inclusive, and may cache other values outside of this range. 
* 
* @param i an {@code int} value. 
* @return an {@code Integer} instance representing {@code i}. 
* @since 1.5 
*/ 
public static Integer valueOf(int i) { 
    if (i >= IntegerCache.low && i <= IntegerCache.high) 
        return IntegerCache.cache[i + (-IntegerCache.low)]; 
    return new Integer(i); 
}
```


Java는 `-128` 부터 `127` 범위의 정수 객체를 캐싱합니다. 
왜냐하면, 이 범위의 정수들은 개발할 때 빈번하게 사용되기 때문입니다.

![Integer Cache Source Code](/static/assets/img/posts/integercache/integercache.jpeg)

캐시는 static block에 의해 IntegerCache 클래스가 메모리에 로드되는 처음에 초기화됩니다. 
캐시의 최대값은 JVM 옵션을 통해 다음과 같이 조정할 수 있습니다.
```
-XX:AutoBoxCacheMax
```

이러한 캐싱은 `Integer` 객체에만 적용되는 것이 아닙니다. 
`Integer.IntegerCache` 처럼, `ByteCache`, `ShortCache`, `LongCache`, `CharacterCache`도 각각 존재합니다.

Byte, Short, Long 타입은 -127부터 127까지의(-127<=, <=127) 고정된 캐시값을 가집니다.
하지만, Character는 0부터 127(0<=, <=127)까지의 고정된 캐시값을 가집니다.
범위를 수정하는 것은 Integer만 수정할 수 있으며, 다른 타입에는 수정이 불가능합니다.

다음 git repository에서 본문의 전체 소스코드를 참조할 수 있습니다. 값진 피드백 부탁드립니다.
[Github Repository](https://github.com/njnareshjoshi/exercises/blob/master/src/org/programming/mitra/exercises/IntegerCacheExample.java)

