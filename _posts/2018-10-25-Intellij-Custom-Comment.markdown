---
layout: post
title:  "[Kotlin] Intelli J Custom Comment (TODO, FIXME, ...)"
date:   2018-10-25 09:00:00
tags: kotlin java ide intellij custom commment customcomment 주석 androidstudio
categories: devstory
---
이번 시간에는 인텔리J에서의 `커스텀 주석` 에 대해서 알아보겠습니다.
정확하게는 Highlighting Custom Comment 라는 문구가 잘 맞겠네요.

예를 들어 우리는 다음과 같은 코멘트들을 많이 사용하고 있습니다.

```
// TODO: Interface 구현 할 것.
// FIXME: 해당 부분은 오류가 발생할 여지가 있음. 수정 할 것.
```

이러한 코멘트들을 잘 사용하면 "생산성" 측면에서나 "공유" 측면에서 좋은 결과를 보여줍니다. 그런데, 해당 주석들만 사용하기에는 너무 부족하지 않으신가요?

Custom 주석을 설정하는 법을 이번시간에는 알아보겠습니다.


예제에서는 Android Studio 로 설명합니다.

### 1. IntelliJ의 TODO 탭을 엽니다.
![1.png](/static/assets/img/posts/customcomment/1.png)

만약 TODO 탭이 없다면, 메뉴의 `View -> Tools Windows -> TODO`를 찾아서 열어줍니다.

### 2. Filter TODO Items를 클릭합니다.
***깔때기*** 모양의 Filter 아이콘을 눌러줍니다. 그 후, `Edit Filters` 버튼을 눌러줍니다.
![2.png](/static/assets/img/posts/customcomment/2.png)

### 3. 자신의 주석을 추가해줍니다.
![3.png](/static/assets/img/posts/customcomment/3.png)
저의 경우에는 `OPTIMIZE` 라는 주석을 "최적화"가 필요한 곳에 사용하기 위해 `\boptimize\b.*`를 사용하였습니다.

아래의 필터쪽에 자신만의 패턴/환경을 세팅해놓고 사용하실 수 있습니다.
해당 환경선택은 Filter 아이콘 선택 후 고를 수 있습니다.


### 4. 사용해봅시다.
![4.png](/static/assets/img/posts/customcomment/4.png)



간단하게 자신만의 주석을 추가 할 수 있습니다.




## References
- Jetbrains : https://www.jetbrains.com/help/idea/
    - 위 페이지에는 재미있는 기능들이 많이 소개되어있습니다. 하나하나 읽어보시는 것도 참 재밌을거에요 !