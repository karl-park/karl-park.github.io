---
layout: post
title:  "[Kotlin] Scoping Functions - apply, run, let, also, with"
date:   2019-07-03 09:00:00
tags: android kotlin scoping function apply run let also with
categories: devstory
---

안녕하세요. 오랜만에 블로그 글을 쓰는 것 같네요.
요새 코틀린을 현업 프로젝트에 적용하며 역시 "기초"가 중요하다는 사실을
계속해서 깨닫고 있습니다.

고로, 이번 시간에는 Kotlin에서 제공하는 몇가지 "스코핑 제어" 함수들을 살펴보도록 하겠습니다.

해당 함수들의 "설계 의도"와 "올바른 쓰임새"를 알고나면, 코틀린으로 개발하기가 더욱
신나고 재밋어지리라 확신합니다 :)

# Kotlin Scoping Functions : apply, run, let, also, with
Kotlin에서는 스코프를 제어하기 위한 함수들이 몇가지 제공됩니다.
**apply, run, let, also, with** 가 대표적인 함수들입니다.

아래에서 하나하나씩 살펴보도록 하겠습니다.

시작하기 전에 하나만 먼저 짚고 넘어갑시다.
`scope 제어`가 목적이기 때문에 `중첩해서 해당 함수들을 사용하는 것`은 올바른 방법이 아닙니다.

자, 이제 거두절미하고 ! 하나하나씩 살펴봅시다.

# apply
`lambda with receiver`(수신 객체 지정 람다)에서 `receiver`(수신 객체)의 함수를 사용하지 않고, 수신 객체 자신을 다시 반환하려는 경우에 **apply**를 사용합니다.

즉, receiver 객체의 "프로퍼티"만을 사용하고, 다른 메소드는 호출하지 않는 경우에 사용합니다. 대표적인 예로 객체 초기화때 주로 사용됩니다.

`반환 값은 receiver 자기 자신입니다.`

### Definition
```kotlin
// T: receiver
inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}
```

### AS-IS
```kotlin
val student1 = Student()
student1.name = "Karl"
student1.age = 30
```

### TO-BE
```kotlin
val student1 = Student().apply {
    // apply 람다 내부에서는 프로퍼티만 접근하도록 합시다.
    name = "Karl"
    age = 30
}
```


# run
run은 apply와 비슷한 사용새를 가집니다.  다만, 반환값이 "receiver" 자기자신이 아니라 람다 호출의 결과인 점이 다릅니다.
그래서, 주로 어떤 값을 계산하는데 사용합니다.

`반환 값은 람다 블록의 호출 결과입니다.`

### AS-IS

```kotlin
val user: User = getUser()
val userDao: UserDao = getUserDao()
val isInsertSuccess: Boolean = userDao.insert(user)

fun printName(user: User) = {
    print(user.name)
}
```

### TO-BE
```kotlin
val isInsertSuccess: Boolean = run {
    val user: User = getUser()
    val userDao: UserDao = getUserDao()
    userDao.insert(user)
}

fun printAge(user: User) = user.run {
    print(agnamee)
}
```


# let

**let** 은 주로, "local variable"의 스코프를 제한하는 경우에 사용됩니다.

호출되는 객체가 null이 아닐 때 실행되어야 할 구문이 있을 때 사용되며, Nullable 객체를  다른 Nullable 객체로 변환 할 때에도 주로 사용됩니다.

`반환 값은 람다 블록의 호출 결과입니다.`

### Definition
```kotlin
inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}
```

### AS-IS
```kotlin
// 어뷰징 유저를 찾고, 만약 존재한다면 이용정지 처리를 합니다.
val badUser: User? = getAbusingUser()
if (badUser != null) {
  ban(badUser)
}

// 좋은 활동을 한 유저를 가져와서, 만약 존재한다면, 알맞은 "보상"을 가져옵니다.
val goodUser: User? = getGoodUser()
val reward: Reward? = if (goodUser == null) {
        null
    } else {
        rewardService.getProperReward(goodUser)
    }

// 특정 유저를 가지고 와서, DAO를 통해 DB에 insert합니다.
val user: User = getUser()
val userDao: userDao = getUserDao()
userDao.insert(user)
```

### TO-BE
```kotlin
// 어뷰징 유저를 찾고, 만약 존재한다면 이용정지 처리를 합니다.
getAbusingUser()?.let {
    // nonnull 일 때 실행됩니다.
    ban(it)
}

// 좋은 활동을 한 유저를 가져와서, 만약 존재한다면, 알맞은 "보상"을 가져옵니다.
val reward: Reward? = getGoodUser()?.let {
    rewardService.getProperReward(it)
}

// 특정 유저를 가지고 와서, DAO를 통해 DB에 insert합니다.
val user: User = getUser()
getUserDao().let { dao -> 
    // 로컬 변수 dao 의 범위를 해당 람다 내부로 제한합니다.
    dao.insert(person)
}
```



# also
`lambda with receiver`(수신 객체 지정 람다) 내부에서 전달된 `receiver`(수신 객체) 자체를 사용하지 않거나, 프로퍼티를 변경하지 않고 사용만 하려는 경우에 **also**를 사용합니다.

주로, 객체 프로퍼티 유효성 검사 등에 사용됩니다.

`반환 값은 receiver 자기 자신입니다.`

### Definition
```kotlin
// T: receiver
inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}
```


### AS-IS
```kotlin
class User(val characters: List<Character>) {
    init {
        requireNotEmpty(characters)
        print(characters[0].name)
    }
}
```

### TO-BE
```kotlin
class User(characters: List<Character>) {
    val characters = characters.also {
      requireNotEmpty(it)
      print(it[0].name)
    }
}
```



# with
NonNull `receiver`(수신객체)이고, 그 결과가 필요하지 않은 경우에 사용됩니다.

`반환 값은 람다 블록의 호출 결과입니다.`

### Definition
```kotlin
inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}
```

### AS-IS

```kotlin
val user: User = getUser()
print(user.name)
print(user.age)
```

### TO-BE
```kotlin
val user: User = getUser()
with(user) {
    print(name)
    print(age)
}
```


# Reference
- [Kotlin Scoping Functions apply vs. with, let, also, and run](https://medium.com/@fatihcoskun/kotlin-scoping-functions-apply-vs-with-let-also-run-816e4efb75f5)