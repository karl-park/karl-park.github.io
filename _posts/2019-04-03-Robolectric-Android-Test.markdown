---
layout: post
title:  "[Android] Robolectric - Android Unit Testing Framework"
date:   2019-04-03 09:00:00
tags: android kotlin java robolectric test coverage testcoverage
categories: devstory
---

## TOC
- 서론
- 1. Robolectric 시작하기
- 2. Configuration 변경
- 3. Shadow? Shadow!
- 4. Best Practice
- 맺으며
- Reference


---

# 서론

안녕하세요. 이번 시간에는 **Android Test Framework** 인 **Robolectric** 에 대해서 다뤄보도록 하겠습니다.
Robolectric을 설명하기전, 간략하게 Android 테스트 종류를 살펴보고, **왜 Robolectric** ~~을 사용해야하는지~~이 좋은지에 대해서 살펴보도록 합시다.

(중간중간, 자바와 코틀린을 섞어서 예제를 구성했습니다. **직접**해보시기를 바라는 마음에 섞어썼으니 이해부탁드립니다.)

## Android Test
Android 개발을 하다보면, 자연스레 "테스트"에 눈이 가게 되는데요, 안드로이드 테스트는 크게 다음의 2가지가 있습니다.

- **Unit Test**
- **Instrumentation Test**

<font style="color:red">**Unit Test**</font> 는 말 그대로 "단위 테스트"를 의미합니다. 흔히 TDD로 개발하게 되면, 매 "기능 단위별"로 테스트 코드를 구성하게 되고, 그 테스트 코드를 통과하는 실제 코드를 작성하곤 하지요. 혹은 그 반대도 가능하구요. 이때 사용되는 테스트 코드가 Unit Test 입니다. 
해당 테스트를 수행하기 위해서는 <font style="color:gray">***module-name/src/test/java/*** </font> 하위에 테스트 코드를 작성하면 됩니다.

<font style="color:red">**Instrumentation Test**</font> 는 실제 하드웨어 기기나 에뮬레이터에서 실행되는 테스트입니다. 안드로이드 환경에서 테스트하기 때문에 실제 [Instrumentation API](https://developer.android.com/reference/android/app/Instrumentation)에 접근 가능합니다. Android 환경에서 실행되는 **AndroidJUnitRunner**를 통해서 실행됩니다. 
해당 테스트는 <font style="color:gray">***module-name/src/androidTest/java//*** </font> 하위에 테스트 코드를 작성합니다. Instrumentation Test 는 Unit Test에 비해서, 그 속도가 느린 것이 단점입니다. 실제로 앱을 <font style="color:blue">***빌드*** </font> 하고, <font style="color:blue">***배포*** </font> 하며, <font style="color:blue">***실행*** </font> 시키는 과정이 매 테스트마다 포함되기에 그렇습니다.

그렇다면, `이렇게 느린 "Instrumentation Test"를 굳이 사용해야만 할까요?` 
유닛 테스트에서 [Instrumentation API](https://developer.android.com/reference/android/app/Instrumentation)에 접근 할 수 있다면(해당 API를 Mocking할 수 있는 프레임워크가 있다면), 유닛 테스트만으로 충분하지 않을까요?


## tada! Robolectric
위와 같은 질문에 답을 내놓은 것이 바로 **Robolectric 프레임워크** 입니다.
안드로이드 프레임워크에 의존성이 있는 코드들을 이제는 유닛테스트로 작성할 수 있는 것이지요. (*shadow*로 바꿔서 처리)
**즉, Robolectric은 자체적으로 `android.jar`의 행동을 Shadowing합니다. 일반적으로 유닛 테스트에서 Android 코드가 있을때, 참조하는 `android-stubs-src.jar`를 참조하지 않습니다.**

```
조금 더 자세히 설명하면 Robolectric을 이용한 Unit Test 실행시, 밑에서 설명할 RobolectricTestRunner가 JVM을 후킹하여, 
Class Loader가 Android Component를 로드하는 것이 아니라, Robolectric의 Shadow 객체를 로딩하도록 합니다.
```

Robolectric은 실제 디바이스의 센서 동작이나 에러 상황 등을 핸들링해주기 때문에,  해당 기능들에 대해서 충분한 단위 테스트가 가능하도록 해줍니다. *<b style="color: #ff0000">(아니 ! 센서까지?)</b>*

<br/>
예를 들어 봅시다.

우리가 개발한 ButtonActivity는 내부의 Button을 클릭하면, "**Hello, Robolectric!**" 라는 문구가 TextView에 찍히게 됩니다.
이러한 로직을 다음과 같은 ButtonActivityTest Unit Test를 통해서 검증할 수 있습니다.
```kotlin
// 실제 ButtonActivity에서의 로직상, Button 클릭을 하면 TextView 텍스트가 "Hello, Robolectric!"로 변경됩니다.

@RunWith(RobolectricTestRunner::class)
class ButtonActivityTest {
    @Test
    fun `버튼을 클릭하면, TextView 문구가 변경되어야함`() {
        // Activity Mocking
        val activity = Robolectric.setupActivity(ButtonActivity::class.java) // Activity를 생성하고 onCreate 콜백을 호출해줍니다.
        val  button = activity.findViewById<Button>(R.id.button) // Button 리소스 id를 이용해 Button 객체를 가져옵니다.
        val textView = activity.findViewById<TextView>(R.id.textView) // TextView 리소스 id를 이용해 TextView 객체를 가져옵니다.

        // Button Click
        button.performClick() // 버튼을 클릭합니다. 즉, 사용자의 행동을 시뮬레이팅합니다.
   
        // Test Code
        val expectedText = "Hello, Robolectric!" 
        val actualText = textView.text.toString() // 실제 TextView의 text를 가져옵니다.
        assertEquals(expectedText, actualText) // 기대값과 실제 결과가 같은지 체크합니다.
    }
}
```

위와 같이 `버튼 동작에 대한 단위 테스트 코드`를 간단하게 작성한것을 볼 수 있습니다. 
이제부터 하나 하나씩 Robolectric을 자세히 살펴보겠습니다.




# 1. Robolectric 시작하기
Robolectric은 Gradle 및 Bazel에서 매우 잘 동작합니다. Bazel 같은 경우는 제가 경험이 없어서, 공식 홈페이지에 있는 내용으로 대체하겠습니다.

- Bazel 설정 살펴보기 : [Robolectric - Building with Bazel](http://robolectric.org/getting-started/#building-with-bazel)
- 그외 빌드 시스템에 적용하기
    - [Robolectric - Building with Buck](https://buckbuild.com/rule/robolectric_test.html)
    - [Robolectric - Building with Older Android Studio/Gradle Versions](http://robolectric.org/other-environments/#android-studio--gradle-agp--30)
    - [Robolectric - Building with Maven & Eclipse](http://robolectric.org/other-environments/#maven--eclipse)

## Gradle 설정

### build.gradle에 다음 구문 추가
**includeAndroidResources** 를 통해서, 실제 앱에 포함되는 리소스를 unit test 경로에서도 참조하여 사용할 수 있게 합니다.
```gradle
dependencies {
  // 글쓰는 시점 기준 최신 버전입니다.
  testImplementation 'org.robolectric:robolectric:4.2'
}

android {
  testOptions {
    unitTests {
      includeAndroidResources = true
    }
  }
}
```

### Test class에 Annotation 추가(RobolectricTestRunner)
```kotlin
@RunWith(RobolectricTestRunner::class)
class MainActivityTest {
    ...
}
```


# 2. Configuration 변경
Robolectric은 런타임에 많은 동작을 설정/변경 할 수 있습니다. 
`robolectric.properties` 파일을 패키지 레벨 경로에 두거나, 클래스/메소드 레벨에서 `@Config` 어노테이션을 사용하여 설정을 할 수 있습니다.


## @Config Annotation
예를 들어, 테스트 하고자하는 특정 클래스에 `@Config` 어노테이션을 달아서 다음과 같이 설정 할 수 있습니다.

```java
@Config(
    sdk = { JELLYBEAN_MR1, KITKAT },
    manifest = "some/build/path/AndroidManifest.xml",
    application = MyCustomApplicatoin.class,
    resourceDir = "some/build/path/res",
    shadows = { ShadowFoo.class, ShadowBar.class } // Shadow 관련해서는 다음장, 3. Shadow? Shadow! 에서 다룹니다.
)
public class MainActivityTest {
    ...
    @Config(
        sdk = KITKAT,
        qualifiers = "fr-xlarge"
    )
    public void test_only_kitcat() {
        ...
    }
}
```


## robolectic.properties File
적용하고자 하는 패키지 경로에 `robolectric.properties` 파일을 작성합니다.
다음과 같이 작성하면, sdk는 18(Android 4.3, JELLY_BEAN_MR2)로 설정되어, 테스트 코드가 실행되게 됩니다.
물론 manifest를 설정하여, "테스트용" manifest를 설정해 줄 수도 있습니다.

```properties
# src/test/resources/com/mycompany/app/robolectric.properties
sdk=18
manifest=some/build/path/AndroidManifest.xml
shadows=my.package.ShadowFoo,my.package.ShadowBar
```


## Global Configuration
동일한 설정을 매 패키지, 클래스, 메소드마다 해주는 것은 어쩌면 번거로운 일일것입니다. 
이 시간을 줄이기 위해서는 Robolectric Test Runner를 상속받아 overriding함으로서, 전역 설정을 해줄 수 있습니다.

```kotlin
class MyTestRunner: RobolectricTestRunner() {
    override fun buildGlobalConfig() {
        // TODO: 전역을 위해 이곳을 overriding합니다.
    }
}
```

위와 같이 Custom Robolectric Test Runner를 만든 후, Annotation을 이용하여 MyTestRunner를 Test Runner로 설정합니다.
```java
@RunWith(MyTestRunner.class)
public class MainActivityTest {
    ...
}
```


## Device Configuration
위에서 살펴본, `@Config` Annotation의 `qualifiers` 프로퍼티를 통해서 Android Device Configuration을 조작 할 수 있습니다.

- [Android - QualifierRules](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)에 해당하는 값들을 `qualifiers` 프로퍼티에 적어주게 되면 Robolectric은 적절하게 파싱하여 Device Configuration을 설정해줍니다.

- [Android - QualifierRules](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) 와 [Robolectric Device Configuration](http://robolectric.org/device-configuration/#specifying-device-configuration) 을 참고하여 `qualifiers` 프로퍼티 값을 다음 예시처럼 입력합니다.

```java
@RunWith(MyTestRunner.class)
@Config(qualifiers = "xlarge-port")
public class MainActivityTest {
  public void testItWithXlargePort() { ... } // config is "xlarge-port"

  @Config(qualifiers = "+land")
  public void testItWithXlargeLand() { ... } // config is "xlarge-land"

  @Config(qualifiers = "land")
  public void testItWithLand() { ... } // config is "normal-land"
}
```

위와 같이 화면 해상도, orientation 등을 설정할 수 있으며, 이에 따른 테스트를 수행할 수 있음을 볼 수 있습니다.




# 3. Shadow? Shadow!
위에서 이미 설명했듯, Robolectric은 Android runtime environment를 제공하므로, Unit Test시에도 "실제 디바이스에서 동작하는 듯한 경험"을 제공할 수 있습니다. 
하지만, 분명 ***한계점*** 도 존재합니다.

1. Native Code
    - Android Native code들은 Robolectric(Test가 실행되는 development machine) 환경에서 실행되지 못합니다.
2. Out of process calls
    - Robolectric이 실행되는 환경에는 Android system service가 없습니다.
3. Inadequate testing APIs
    - Android는 테스팅에 적합한 API들을 가지고 있지 않습니다.

위와 같은 제약사항들을 메꾸기 위해, Robolectric은 <font style="color:red">**Shadow** </font> 라는 클래스들을 제공합니다. Shadow들은 Android 클래스에 맞게 그 동작을 확장하고 변경할 수 있습니다. Android 클래스가 초기화되면, Robolectic은 적합한 *shadow class*를 찾고, 생성하여 연결합니다.

Robolectric은 Byte code를 조작함으로써, native code들과 테스트 가능한 API들에 대한 fake implementation을 만들 수 있습니다.



## Shadow? 이름의 뜻
Shadow는 [Proxies](http://en.wikipedia.org/wiki/Proxy_pattern)도 아니고 [Fakes](http://c2.com/cgi/wiki?FakeObject)도 아닙니다. 그렇다고 [Mock이나 Stub](http://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs)도 아닙니다. **Shadow**는 감춰지기도 하며, 때로는 보이기도 합니다. 그리고 실제 객체를 가리키기도 합니다. 그렇기에 **Shadow** 라는 이름을 붙였다고 합니다.


## Shadow Classes
Shadow class는 argument가 한개도 없는 public 생성자를 필요로 합니다. 해당 생성자를 통해 Robolectric은 Shadow class를 생성합니다.
생성된 클래스는 `@Implements` 어노테이션이 붙은 클래스와 연결되어서 동작합니다.

```java
// TextView에 대한 Shadow 클래스
@Implements(TextView.class)
public class MyShadowTextView extends ShadowView {
    // 다음과 같이 선언을 해주던가, 아니면 선언을 하지 않는다. (컴파일러가 기본 생성자를 자동으로 생성하기 때문)
    public MyShadowViewGroup() {
        super();
    }
}
```


## Shadowing Methods
Shadow 객체는 Android 클래스와 동일한 signature를 가진 메소드들을 구현해야합니다. Robolectric은 Android 객체가 invoke될 때, 동일한 signature를 가지는 Shadow method를 호출해줍니다.
메소드에는 `@Implementation` 어노테이션을 붙여줍니다. (`protected` modifier를 붙여주어야 하는 것에 유의하세요)

예를 들어 다음과 같은 어플리케이션 로직이 있다고 합시다.
```java
...
myTextView.setText("Hello");
...
```

위의 TextView의 Shadow 및 Shadow method를 작성해보면 다음과 같습니다.

```java
// TextView에 대한 Shadow 클래스
@Implements(TextView.class)
public class MyShadowTextView extends ShadowView {

    @Implementation
    protected void setText(CharSequence text) {
        // TODO: Shadowing 구현 
    }
}
```
더 재밋는 점은 Robolectric은 원래 클래스의 `public, protected, private, static, final, native` modifier 모두를 Shadowing 할 수 있다는 점입니다.

<br/>

> 주의! Shadow를 쓸 때 명심해야할 점이 하나 있습니다.
> Shadowing하려는 method는 해당 method가 정의(define)되어있는 원래 클래스와 부합하는 Shadowing 클래스에서 사용되어야합니다. (말이 어렵네요.. 예를 보시죠 !)
>
> 예를 들어, setEnabled() 메소드는 View 클래스에 정의되어있습니다. 만약, setEnabled() 메소드가 ShadowView 대신 ShadowViewGroup을 상속한 클래스에 대해 @Implementation 되어서 사용되고 있다면, Robolectric은 런타임에 해당 메소드를 찾지 못합니다.
> ViewGroup에도 setEnabled() 메소드가 정의되어있지만, original 클래스가 아니기 때문이죠.


    

## Shadowing Constructors
생성자 또한 Shadowing 할 수 있습니다. 메소드를 Shadowing하는 것과 동일한 signature로 작성하면 됩니다. 
대신 메소드명은 **`__constructor__`** 이어야 한다는 것에 주의하세요.

예를 들어 봅시다.
```java
new TextView(context);
```

위와 같은 TextView에 대한 Shadow 클래스 및 생성자를 살펴봅시다.
```java
// TextView에 대한 Shadow 클래스
@Implements(TextView.class)
public class MyShadowTextView extends ShadowView {

    @Implementation
    protected void __constructor__(Context context) {
        this.context = context;
    }
}
```

Shadow 에 대해 더 자세한 설명은 아래 링크를 참고해주시길 바랍니다.
- [Robolectric 공식 홈페이지 Shadow 설명](http://robolectric.org/extending/)



# 4. Best Practice
Robolectric 에서는 다음 4가지를 Best Practice 로 제시하고 있습니다.

- **`DON’T`**
    - 다른 Android code의 동작에 의해 변경되는 Android 클래스를 mocking, spying 하지마세요. (예를 들어, Context, SharedPreferences 와 같은 클래스들이 있습니다.)
    - 명확하고 작은 책임만을 가지는 Listener에만 mocking, spying하세요.

- **`DO`** 
    - Robolectric으로 Layout Inflation 테스트를 할 때, Activity와 Layout 사이의 인터렉션이 직접 이뤄지게하여, 클릭 리스너들을 올바르게 세팅하도록 하세요. 
    - LayoutInflater를 mocking하거나, View에 대한 추상화를 통해 테스트하지 마세요.

- **`DO`** 
    - public Lifecycle APIs(예) Robolectric.buildActivity()를 통해 제공되는 API들)를 사용하세요. 
    - Activity나 Service 등과 같은 Android Component를 테스트 할 때, @VisibleForTesting 을 이용한 테스트 목적의 메소드를 이용하지 마세요.
    - 즉, @VisibleForTesting 과 같은 테스트 목적 메소드들은 추후, 테스트 코드를 리팩토링 할 때 큰 어려움이 될 수 있습니다.

- **`DO`** 
    - 각 테스트가 진행되는 동안 최대 쓰레드 개수를 제한하세요. 테스트 사이 사이에 GC되지 않은 쓰레드들이 남아있을 수 있어서, 테스트 환경을 오염시킬 수 있습니다. 
    - 3rd Party Library에서 쓰레드들이 생성될 수 있는데, 이렇게 쓰레드들을 생성하는 컴포넌트들에 대해서는 mocking을 하세요. 혹은, **DirectExecutor**을 사용 할 수도 있습니다.
    - 만약 꼭, 여러개의 쓰레드를 테스트시 사용해야한다면, 명시적으로 모든 쓰레드들과 ExecutorService를 멈추면서 테스트를 하시길 바랍니다.
    
    



# 맺으며
Spring Framework 개발을 할 때, 수많은 mock과 stub들을 만들었던 기억이 있습니다. 여러 환경을 모사하고 테스트를 했었는데요, Android의 경우에는 개발 환경에서 Android 런타임을 재현하기가 까다로와서, QA 분들에게 테스트를 맡기거나, 개발자 테스트로 대체하곤 했습니다.

하지만, Robolectric을 접하고서는 이전보다 보다 빠르고 안정적인 기능 단위 테스트를 작성 할 수 있게되었다고 생각합니다. 
이러한 Robolectric을 여러분의 프로젝트에도 적용하여, 프로젝트의 stability를 높이는데 사용하면 어떨까요?




# Reference
- 공식 홈페이지 : http://robolectric.org/