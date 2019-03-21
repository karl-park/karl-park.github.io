---
layout: post
title:  "[Android&Kotlin] Prevent Robolectric Error When mesuring Test Coverage"
date:   2019-03-15 09:00:00
tags: android kotlin java robolectric test coverage testcoverage
categories: devstory
---

안녕하세요. 이번 시간에는 Robolectric 사용 및 테스트 커버리지 측정시 생기는 에러에 대해서 짧게 다뤄보겠습니다.

많이들 아시다 시피, Robolectric Framework는 Android Unit test 코드를 빠르고 신뢰할 만하게 수행할 수 있도록 도와주는 Framework 입니다.

Robolectric에 대해 자세한 사항은 [Robolectric 홈페이지](http://robolectric.org/)를 참고부탁드립니다.
(조만간 Robolectric에 대해서도 글을 쓸 계획입니다 :) )


---

위에 설명한대로, Robolectric은 Android에서의 Unit test 를 작성하게 도와줍니다. Unit Test 작성 후, 많은 개발자들은 Coverage 측정을 해보는데요~
Robolectric을 있는 그대로 사용하다보면 다음과 같은 에러 로그를 보실 수 있습니다. 물론, 에러로그와 함께, Robolectric을 사용하여 테스트를 하는 클래스들은 테스트 실패가 떨어지게 됩니다.

> 주의할 점은 "Test Coverage" 측정시에만 실패한다는 것입니다. Run Test, Debug Test에는 해당 에러가 발생하지 않습니다.

에러문은 다음과 같습니다.
```
java.lang.VerifyError: Bad return type
Exception Details:
  Location:
    android/content/res/ResourcesImpl.$$robo$$loadComplexColorForCookie(Landroid/content/res/Resources;Landroid/util/TypedValue;ILandroid/content/res/Resources$Theme;)Landroid/content/res/ComplexColor; @565: areturn
  Reason:
    Type 'java/lang/Object' (current frame, stack[0]) is not assignable to 'android/content/res/ComplexColor' (from method signature)
  Current Frame:
    bci: @565
    flags: { }
    locals: { 'android/content/res/ResourcesImpl', 'android/content/res/Resources', 'android/util/TypedValue', integer, 'android/content/res/Resources$Theme', 'java/lang/Object', 'java/lang/String', 'java/lang/Object', 'android/content/res/XmlResourceParser', 'android/util/AttributeSet', integer, 'java/lang/String' }
    stack: { 'java/lang/Object' }
  Bytecode:
    0x0000000: 127c b800 823a 0519 0511 041b b800 862c
    0x0000010: b401 a9c7 002a 1905 1104 1cb8 0086 bb04

......
......
```

```
java.lang.IllegalStateException: this method should only be called by Robolectric
	at org.robolectric.shadows.ShadowDisplayManager.configureDefaultDisplay(ShadowDisplayManager.java:43)
	at org.robolectric.android.Bootstrap.setUpDisplay(Bootstrap.java:19)
	at org.robolectric.android.internal.ParallelUniverse.setUpApplicationState(ParallelUniverse.java:159)
    ...
    ...
```

---

위와 같은 에러를 만났다면 다음과 같은 추가 설정이 필요합니다.

**Test Configuration**의 **VM Options**에 다음을 입력합니다.
- `-noverify`

다음과 같이 말이죠.

![test coverage with robolectric](/static/assets/img/posts/robolectric-testcoverage/configuration.png)

--

[jetbrains code coverage](https://www.jetbrains.com/help/idea/code-coverage.html#intro) 사이트에 보면, EMMA 와 같은 Test runner를 쓸때는 -noverify 로 설정을 해줘야한다고 설명이 되어있는데요, Robolectric에 대한 언급은 없지만, 이 역시 같은 설정을 해줘야한다고 생각합니다.