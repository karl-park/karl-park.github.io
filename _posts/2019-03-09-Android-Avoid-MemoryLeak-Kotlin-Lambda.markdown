---
layout: post
title:  "[Java&Kotlin] Avoiding Memory Leak"
date:   2019-03-09 09:00:00
tags: kotlin java android memory leak memoryleak sam lambda anonymous anonymousclass
categories: devstory
---
안녕하세요.
이번 시간에는 제가 사랑하는 **Kotlin**과 **Lambda/SAM**가 제공하는 수많은 장점 중, **`memory leak`** 을 회피하는 것에 대해서 살펴보고자 합니다.

안드로이드 중심으로 쓰여졌으나, 일반적인 JVM에도 적용되니 Java/Kotlin 개발자는 많은 도움을 얻을 수 있을 것 같습니다 !


본문에 쓰인 모든 코드는 제 깃헙에 올려두었으니, 받으셔서 직접 테스트 하실 수 있습니다.

- [https://github.com/karl-park/MemoryLeakTest.git](https://github.com/karl-park/MemoryLeakTest.git)


# Java, Anonymous Class and Memory Leak
다음과 같은 **Java** 로 개발된 Activity가 있습니다.

```java
public class MyJavaLeakActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my_java_leak);

        startAsyncTask();
    }

    private void startAsyncTask() {
        Runnable task = new Runnable() {
            @Override
            public void run() {
                SystemClock.sleep(20000);
            }
        };
        new Thread(task).start();
    }
}
```

위의 Activity가 생성되면(onCreate), **startAsyncTask()** 메소드에 의하여, 새로운 스레드가 생성되어 동작을 합니다. 

해당 task는 20초 동안 sleep을 하게 됩니다. (Thread.sleep을 쓰지 않은 이유는 Thread.sleep은 interupt로 인해 종료될 수 있기 때문입니다.) 즉, 위에 새로 생성되는 Runnable 객체는 약 20초간 프로세스에 머무르겠지요.

여기서 우리가 주목할 점은 생성되는 **`Anonymous Runnable`** (익명 클래스) 객체가 **`MyJavaLeakActivity에 대해 hidden outer reference를 가지게 된다`** 는 점입니다. 

그래서, MyJavaLeakActivity가 destroyed 되더라도, Runnable 객체가 살아있음으로 인해, ***Garbage Collected*** 되지 못하고,(최대 20초 동안) MyJavaLeakActivity는 memory leak을 유발시킵니다. 물론, 20초 후에는 GC에 의해 메모리에서 제거되겠지요.

위의 Java로 쓰여진 MyJavaLeakActivity에 대한 **`Bytecode`** 를 살펴봅시다.
(빌드되어진 apk 파일을 다음과 같이 열어보시면 확인하실 수 있습니다.)

![smali bytecode](/static/assets/img/posts/lambda-memleak/smali.png)


먼저, **MyJavaLeakActivity** 파일의 Bytecode를 봅시다. 
(위 스크린샷에서 **MyJavaLeakActivity$1 파일**도 생성되어 있다는 것에 집중하세요! 나중에 살펴볼꺼에요.)
(+ Bytecode 중, 본문 내용과 크게 관련이 없는 부분은 의도적으로 생략하였습니다.)

```smali
.class public Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;
.super Landroid/support/v7/app/AppCompatActivity;
.source "MyJavaLeakActivity.java"


# direct methods
.method public constructor <init>()V
    .registers 1

    .line 7
    invoke-direct {p0}, Landroid/support/v7/app/AppCompatActivity;-><init>()V

    return-void
.end method

.method private startAsyncTask()V
    .registers 3

    .line 17
    new-instance v0, Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;

    invoke-direct {v0, p0}, Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;-><init>(Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;)V

    .line 23
    .local v0, "task":Ljava/lang/Runnable;
    new-instance v1, Ljava/lang/Thread;

    invoke-direct {v1, v0}, Ljava/lang/Thread;-><init>(Ljava/lang/Runnable;)V

    invoke-virtual {v1}, Ljava/lang/Thread;->start()V

    .line 24
    return-void
.end method
...

```

<br/>

여기서 우리가 집중해서 봐야할 곳은 바로 이 부분(**startAsyncWork** 메소드 부)입니다.

```smalltalk
new-instance v0, Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;

invoke-direct {v0, p0}, Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;-><init>(Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;)V
```

우리는 익명 클래스 객체가 생성될 때, 외부 클래스의 참조를 가지고 있다는 것을 알고 있습니다. 
여기에서는 `MyJavaLeakActivity$1` 인스턴스를 생성하여 **v0** 변수에 저장하고, 해당 값을 이용하여, MyJavaLeakActivity의 초기화를 진행하는 것을 볼 수 있습니다.

<br/>
<br/>
> ***마치 Inner Class가 Outer Class의 참조를 가지고 있는 것처럼, 익명 클래스 인스턴스 또한 외부 클래스 인스턴스의 레퍼런스를 가지고 있습니다.***
<br/>
<br/>

---

MyJavaLeakActivity$1 클래스를 살펴봅시다.

아래는 **MyJavaLeakActivity$1** 파일의 **Bytecode** 입니다.
```smali
.class Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;
.super Ljava/lang/Object;
.source "MyJavaLeakActivity.java"

# interfaces
.implements Ljava/lang/Runnable;


# instance fields
.field final synthetic this$0:Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;


# direct methods
.method constructor <init>(Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;)V
    .registers 2
    .param p1, "this$0"    # Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;

    .line 17
    iput-object p1, p0, Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity$1;->this$0:Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;

    invoke-direct {p0}, Ljava/lang/Object;-><init>()V

    return-void
.end method


# virtual methods
.method public run()V
    .registers 3

    .line 20
    const-wide/16 v0, 0x4e20

    invoke-static {v0, v1}, Landroid/os/SystemClock;->sleep(J)V

    .line 21
    return-void
.end method
```

여기서 우리가 주목할 것은, 아래 구문입니다.

```smali
# interfaces
.implements Ljava/lang/Runnable;

...

# instance fields
.field final synthetic this$0:Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;
```

이 클래스(`MyJavaLeakActivity$1`)는 Runnable 인터페이스를 구현했군요. MyJavaLeakActivity 타입의 필드를 가지고 있으며, 아래 생성자를 통해 MyJavaLeakActivity 인스턴스를 받고 있습니다.

```smali
# direct methods
.method constructor <init>(Lcom/eungpang/android/memoryleaktest/MyJavaLeakActivity;)V
```

즉, 이 클래스(`MyJavaLeakActivity$1`)는 위 Java 코드의 20초 동안 Runnable task를 진행하는 익명 클래스인데요 ~ 외부 클래스(MyJavaLeakActivity) 인스턴스를 생성자로부터 받아서 필드로 가지고 있기 때문에 memory leak을 발생시킵니다.

---

# Kotlin, Lambda and SAM

위의 MyJavaLeakActivity를 그~대로 Kotlin으로 옮겨봅시다.

```kotlin
class MyKotlinLeakActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_my_kotlin_leak)

        startAsyncTask()
    }

    private fun startAsyncTask() {
        val task = Runnable {
            SystemClock.sleep(20000)
        }
        Thread(task).start()
    }
}
```

단순히, Kotlin의 **Lambda/SAM** (Single Abstract Method)을 이용해 포팅하였습니다.
코드만 보자면, 위의 Java코드와 동일하게 동작하리라고 생각할 수 있겠는데요 !! Bytecode를 살펴봅시다 !!!

먼저 **MyKotlinLeakActivity** 클래스 **Bytecode** 입니다.


```smali
.class public final Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity;
.super Landroid/support/v7/app/AppCompatActivity;
.source "MyKotlinLeakActivity.kt"

...

# direct methods
.method public constructor <init>()V
    .registers 1

    .line 7
    invoke-direct {p0}, Landroid/support/v7/app/AppCompatActivity;-><init>()V

    return-void
.end method

.method private final startAsyncTask()V
    .registers 3

    .line 17
    sget-object v0, Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;->INSTANCE:Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;

    check-cast v0, Ljava/lang/Runnable;

    .line 20
    .local v0, "task":Ljava/lang/Runnable;
    new-instance v1, Ljava/lang/Thread;

    invoke-direct {v1, v0}, Ljava/lang/Thread;-><init>(Ljava/lang/Runnable;)V

    invoke-virtual {v1}, Ljava/lang/Thread;->start()V

    .line 21
    return-void
.end method

...
```

코드를 보면, Java가 변환된 Bytecode와 다소 차이점이 있는 것을 ~~바로~~ 자세히보면 알 수 있습니다.

바로, 아래 부분인데요 ~ Java에서는 new-instance 를 통해 객체를 생성한 반면(MyJavaLeakActivity$1 객체 생성), Kotlin Bytecode에서는 아래와 같이 **MyKotlinLeakActivity$startAsyncTask$task$1** `static 인스턴스`를 가져와서 **v0** 변수에 저장하는 것을 알 수 있습니다.

```
sget-object v0, Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;->INSTANCE:Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;
```


---


그렇다면 **MyKotlinLeakActivity$startAsyncTask$task$1**  클래스는 어떻게 생겼을까요?
위 smali 코드에 의하면, 자기 자신 인스턴스를 가지는 static 필드가 있겠고, Java 코드와 마찬가지로 Runnable을 구현하겠네요. 정말인지 다음 코드를 확인해보시죠 !

```smali
.class final Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;
.super Ljava/lang/Object;
.source "MyKotlinLeakActivity.kt"

# interfaces
.implements Ljava/lang/Runnable;

...


# static fields
.field public static final INSTANCE:Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;


# direct methods
.method static constructor <clinit>()V
    .registers 1

    new-instance v0, Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;

    invoke-direct {v0}, Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;-><init>()V

    sput-object v0, Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;->INSTANCE:Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;

    return-void
.end method

.method constructor <init>()V
    .registers 1

    invoke-direct {p0}, Ljava/lang/Object;-><init>()V

    return-void
.end method


# virtual methods
.method public final run()V
    .registers 3

    .line 18
    const-wide/16 v0, 0x4e20

    invoke-static {v0, v1}, Landroid/os/SystemClock;->sleep(J)V

    .line 19
    return-void
.end method

```

위 코드에서 주목할 부분은 바로 아래와 같습니다.
```smali
# interfaces
.implements Ljava/lang/Runnable;
...
# static fields
.field public static final INSTANCE:Lcom/eungpang/android/memoryleaktest/MyKotlinLeakActivity$startAsyncTask$task$1;
```

위에서 얘기한대로, **MyKotlinLeakActivity$startAsyncTask$task$1** 클래스는 Runnable을 구현하여 실제 20초를 기다리는 로직을 포함하는 클래스이군요. 또한 싱글턴 패턴처럼, 자기 자신을 가지는 static 필드를 가지고 있습니다. 

즉, 어디에도 `MyKotlinLeakActivity 클래스 인스턴스`는 보이지 않습니다.
이러하여 Kotlin 코드에서는 **memory leak**이 발생하지 않는 것입니다.



## 결론?
결론을 지어봅시다.
`Anonymous inner class`를 사용하면 **Outer Class의 Reference를 가지게 됩니다.** 그래서 memory leak이 발생할 여지가 생기는 것이죠.

반면, `SAM, lambda`를 사용하면, **Static Field**를 생성하여 작업을 진행하므로, 상대적으로 memory leak이 발생할 여지가 줄어들게 됩니다.


> 명심할 점 !
> 사실은 Kotlin이기 때문에 memory leak을 줄일 수 있는 것이 아니라, anonymous inner class를 사용하지 않으므로, memory leak을 예방 할 수 있는 것입니다.
> 
> 즉, Java 코드에서도 `Lambda expression/SAM`을 통해 개발을 하면, 동일한 효과를 볼 수 있습니다.

> 아래 **부록** `"Java에서 Lambda를 사용한다면?"` 에서 Java 코드 및 Smali 코드를 확인하실 수 있습니다.
---


# 부록

## Java에서 Lambda를 사용한다면?

위의 결론에서 이미 언급했듯, Java 코드에서도 Lambda를 사용하여 코드를 작성한다면, Kotlin의 코드처럼, memory leak을 예방 할 수 있습니다 !! 다음은 Java 코드입니다.
```java
public class MyJavaLambdaLeakActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my_java_leak);

        startAsyncTask();
    }

    private void startAsyncTask() {
        Runnable task = () -> {
            SystemClock.sleep(20000);
        };

        new Thread(task).start();
    }
}
```

위 처럼 Java로 개발을 하고, 빌드를 하면 다음과 같은 **smali** 코드가 나옵니다 !

먼저, **MyJavaLambdaLeakActivity Bytecode**입니다.

```smali
.class public Lcom/eungpang/android/memoryleaktest/MyJavaLambdaLeakActivity;
.super Landroid/support/v7/app/AppCompatActivity;
.source "MyJavaLambdaLeakActivity.java"

...

.method static synthetic lambda$startAsyncTask$0()V
    .registers 2

    .line 18
    const-wide/16 v0, 0x4e20

    invoke-static {v0, v1}, Landroid/os/SystemClock;->sleep(J)V

    .line 19
    return-void
.end method

.method private startAsyncTask()V
    .registers 3

    .line 17
    sget-object v0, Lcom/eungpang/android/memoryleaktest/-$$Lambda$MyJavaLambdaLeakActivity$caHB7J9mZR29sol_sLXUURDc30c;->INSTANCE:Lcom/eungpang/android/memoryleaktest/-$$Lambda$MyJavaLambdaLeakActivity$caHB7J9mZR29sol_sLXUURDc30c;

    .line 21
    .local v0, "task":Ljava/lang/Runnable;
    new-instance v1, Ljava/lang/Thread;

    invoke-direct {v1, v0}, Ljava/lang/Thread;-><init>(Ljava/lang/Runnable;)V

    invoke-virtual {v1}, Ljava/lang/Thread;->start()V

    .line 22
    return-void
.end method
...

```

아래는 생성된 **-$$Lambda$MyJavaLambdaLeakActivity$caHB7J9mZR29sol_sLXUURDc30c** 코드입니다.

```smali
.class public final synthetic Lcom/eungpang/android/memoryleaktest/-$$Lambda$MyJavaLambdaLeakActivity$caHB7J9mZR29sol_sLXUURDc30c;
.super Ljava/lang/Object;
.source "lambda"

# interfaces
.implements Ljava/lang/Runnable;


# static fields
.field public static final synthetic INSTANCE:Lcom/eungpang/android/memoryleaktest/-$$Lambda$MyJavaLambdaLeakActivity$caHB7J9mZR29sol_sLXUURDc30c;

...
```

`Kotlin`으로 개발을 했을 때 처럼, static field를 가지고 있으면서 **MyJavaLambdaLeakActivity** 인스턴스를 들고있지 않아서, memory leak을 예방하는 것을 볼 수 있습니다.


## Leak 분석툴 : LeakCanary
- [LeakCanary : A memory leak detection library for Android and Java](https://github.com/square/leakcanary)

- 위의 분석툴을 이용하면, Memory Leak을 검사해볼 수 있습니다. 링크를 참고하시어 테스트해보시면 좋을 것 같아요 : )



원문은 [How Kotlin Helps You Avoid Memory Leaks](https://proandroiddev.com/how-kotlin-helps-you-avoid-memory-leaks-e2680cf6e71e)에서 살펴보실 수 있습니다.



## 모든 소스 코드
- [https://github.com/karl-park/MemoryLeakTest.git](https://github.com/karl-park/MemoryLeakTest.git)


