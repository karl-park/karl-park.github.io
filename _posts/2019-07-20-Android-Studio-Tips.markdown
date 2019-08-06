---
layout: post
title:  "[Android] Android Studio Tips"
date:   2019-07-20 12:00:00
tags: android androidstudio ide tips intellij
categories: devstory
---
# Android Studio Tips

현재 7월 20일 기준, Android Studio는 3.4.2 (macOS) 까지 출시되었습니다.
Preview의 경우에는 3.5.0 Beta5 까지 출시되었네요.

Release Note를 보면, 거의 서너달에 한번씩은 minor 버전이 올라가는 것을 볼 수 있습니다.
이처럼 빠르게 업데이트되는 Android Studio에 개발자 편의를 위한 수많은 기능들이 있는 것을 아시나요?

이번 시간에는 Android Studio에 ~~숨어있는~~(아시는 분들은 다 아시는) 기능들을 살펴보겠습니다.

- Android Studio Download : https://developer.android.com/studio
- Android Studio Release Note : https://developer.android.com/studio/releases/
- Android Studio Preview : https://developer.android.com/studio/preview

## 1. Quick Lists
- 위치 : **Preferences** > **Appearance & Behavior** > **Quick Lists**
- 유용성 : ★★★★☆

**Quick Lists** 를 사용해서, 본인이 자주 사용하는 기능들을 모아서 호출 할 수 있습니다.
**Preferences** > **Appearance & Behavior** > **Quick Lists** 에 들어가서, 본인이 원하는 기능들을 모아서 사용합니다.

그 후, "**cmd** + **shift** + **A**" 키를 이용 및 **Action** 탭에서 본인이 설정한 이름으로 기능을 호출합니다.

한 가지 팁으로는, "**cmd** + **shift** + **A**" 자체도 불편 할 수 있으므로 !! Quick Lists 자체에 HotKey(Keymap)를 설정하여 접근하는 것이 더욱 편리한 Quick Lists 사용법이 됩니다.


![quicklist1.gif](/static/assets/img/posts/androidstudiotips/android-studio-tips-1.gif)
\<Preferences \> Quick Lists\>


![quicklist2.gif](/static/assets/img/posts/androidstudiotips/android-studio-tips-2.gif)
\<Setting Quick Lists in Keymap\>



## 2. Notification
- 위치 : **Preferences** > **Appearance & Behavior** > **Notification**
- 유용성 : ★★★☆☆

특정 작업들을 수행하고 나면, Android Studio 좌하단에서 작업 결과 등을 알려주는 Popup, Balloon 등을 보신적이 있으신가요?
해당 알림을 **Preferences** > **Appearance & Behavior** > **Notification** 에서 설정가능합니다.
만약 nerd 처럼 보이고 싶다면, `Read aloud` 체크를 해보세요. console output을 Android Studio가읽어줍니다 🤣🤣🤣

![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-3.png)




## 3. Code Style Inspection
- 위치 : **Preferences** > **Editor** > **Inspections** > **Kotlin** > **Style Issues** > **File is not formatted according to project settings**
- 유용성 : ★★★★★

여러 팀원들과 협업을 하다보면, 사소한 indenting 이나, convention 등에 시간을 많이 뺏길 때가 있습니다.
사실 중요한건 **로직** 그 자체인데 말이죠. 
띄어쓰기나 indenting 등 file formating 관련하여 **warning 혹은 error** 등을 띄워 줄 수 있는 기능이 있습니다.

해당 기능을 적용하면, 다음 스크린샷처럼, "이상하게 작성된 코드"에 대해 **warning 혹은 error**를 띄워줍니다.
PR 시 다른 얘기를 할 여지를 많이 줄여주겠지요?
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-4.png)

아래 처럼 설정합니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-5.png)
\<Preferences \> Inspections\>

#### Tips!
- 위치 : **Preferences** > **Editor** > **Code Style** > **Formatter Control** > **Enable formatter markers in comments**

팁으로는 "Foramtter"가 적용되면 안되는(이를테면 레거시) 코드들이 있을 수 있습니다. 해당 코드들에는 Formatter 기능을 끌 수 있는데요, 다음과 같이 설정합니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-6.png)

즉, 아래와 같이 Formatting Inspection을 잠시 꺼둘 수 있습니다. 아래 구문은 **warning 혹은 error** 를 띄우지 않습니다.

```kotlin
// @formatter:off
        val  weirdCodeIndenting    :String            = "1"
// @formatter:on

```



## 4. Conditional Break Point
- 위치 : **gutter** -> **add break points** -> 
- 유용성 : ★★★★★

정말 정말 유용합니다. Break point를 통해 debugging을 할 때, 특정 조건에 breaking을 할 수 있도록 해줍니다.
통상적으로 break point를 설정하고, gutter (코드 왼쪽 공간) break point 부분을 오른쪽 클릭하면 다음과 같은 창이 뜹니다.
해당 창에 condition을 적어주면 debugging 모드시 신세계를 경험 할 수 있습니다.

![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-7.png)




## 5. Language Injections
- 위치 : **Preferences** -> **Editor** -> **Language Injections** -> **Advanced**
- 유용성 : ★★★★☆

클라이언트 코드에 JSON 등을 DSL(Domain Specific Language)들을 작성할 일이 간혹 있습니다. 
특히 JSON 통신을 위한 테스트 코드를 짤 때, inlined json string variable을 많이 작성하는데요 ~ 
이 때, 작성하는 String이 **JSON Code(JSON 뿐만 아니라, 다른 언어 포멧도 가능합니다.)** 임을 IDE에 알려주는 방법이 있습니다.

바로 아래와 같이 말이죠 !
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-8.png)

기본적으로는 **org.intellij.lang.annotations.Language** Annotation을 사용합니다.
Annotation을 붙여준 후, value에 해당 DSL 타입을 적어주면 됩니다.

그 후, 수정을 하려면 **Option + Enter** 키를 눌러서, **Edit XXX Fragment** 를 눌러주면 됩니다.


아래와 같은 창을 통해 설정을 할 수도 있습니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-9.png)



## 6. Default Layout Editor
- 위치 : **Preferences** -> **Editor** -> **Layout Editor**
- 유용성 : ★★★★★

Android 앱개발 시, **activity_main.xml** 등의 레이아웃 에디터를 열어서 수정작업을 할 때가 많습니다.
이 때, 아래 그림처럼, "Design"탭이 먼저 노출되어서 안드로이드 스튜디오가 잠시 멈칫! 하며 느려질 때가 있습니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-10.png)

아래처럼 설정해주면, 기본 Layout Editor 창이 **XML Text Editor**로 설정이 됩니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-11.png)




## 7. Live Templates
- 위치 : **Preferences** -> **Editor** -> **Live Templates**
- 유용성 : ★★★★★

java 개발시, 생각보다 **public static final String**이라는 구문을 쓸때가 많은데요 ~ Android Studio에서 다음과 같이 미리 정의되어있다는 사실 아셨나요?
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-12.png)
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-13.png)

즉, **psfs**를 타이핑하게되면, **public static final String**을 바로 칠 수 있습니다. 이러한 기능들은 생각보다 많이 빌트인되어있는데요 ! 
이제 각자 많이 사용하는 커스텀한 Live Templates 를 추가하여 생산성을 높혀봅시다.


## 8. File Template
- 위치 : **Preferences** -> **Editor** -> **File and Code Templates**
- 유용성 : ★★★★★

코드 단에서의 Live Template 뿐만 아니라, "파일 생성시" 사용 할 수 있는 File Template 도 있습니다.
저는 주로, Copyrights, author 등을 나타내기 위해 사용하는데요 ~ 각 클래스 설명 등의 주석이나,
클래스 템플릿 등등에도 활용할 수 있습니다.
![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-14.png)


## 9. Build
- 위치 : **Preferences** -> **Plugins**
- 유용성 : ★★★★★

Android Studio에 기본 내장 Plugin이 많이 있다는 것을 아시나요? 필요없는 Plugin도 많이 Active 된 상태 일 수 있습니다.
아래 설정으로 들어가서, 당장은 필요없다고 여겨지는 Plugin들을 빼면, 보다 더 빠른 동작이 가능합니다.

![image.png](/static/assets/img/posts/androidstudiotips/android-studio-tips-15.png)