---
layout: post
title:  "[iOS] Reject 사유: Non-Public API"
date:   2016-09-19 03:00:00
tags: objc ios AppStore Reject Non-PublicAPI
categories: devstory
---
# Apple Reject
iOS10 으로 업데이트되면서 부터, 우리 팀이 개발하는 iOS SDK를 사용하는 게임/앱들이 Apple에서 `리젝`을 당하고 있다는 충격적인 소식을 들었다.

문제는 바로 이 녀석.

> Apple non-public API


Non-public API란 무엇이길래 AppStore 리젝을 당할까?
하나하나 알아보자.

애플 문서에는 다음과 같이 정의되어있다.

`non-public API` : The non-public API refers to Apple API methods that are not documented and offered to the programmer. Apple does not guarantee that this part of the API will work in future upgrades. They can freely change this part.

정리하자면 !

> Apple에서 사용하는 API로써, 개발자들에게 문서화되어 제공되지 않은 API.
> deprecated 되었거나, 아직 개발중이거나, 내부적으로 사용하는 API 등이 이에 해당됨.




## 리젝 사유
리젝을 당하게 되면, 리젝에 대한 사유가 메일 등으로 오게된다.
우리는 역시 Non-Public API로 리젝을 당했기 때문에, 다음과 같은 사유서가 왔다.(고 한다)


```
...
Your app uses or references the following non-public APIs:

didReceiveMessage:

The use of non-public APIs is not permitted on the App Store because it can lead to a poor user experience should these APIs change.
...
```

위와 같은 메일을 받았으면, `didReceiveMessage:`라는 selector가 문제가 있음을 바로 알 수 있다.


그렇다면 해당 selector를 어디서 사용했는지 어떻게 알 수 있을까?
(다 찾아보고나서지만, 내가 만든 SDK에서는 사용하고 있지 않았고! 3rd party library에서 사용중이었다. 정확히는 오픈소스 웹소켓 라이브러리... OTL)



## Utility for Checking Symbols
이번 리젝사태를 겪으면서, 배운 3가지 유틸리티 툴은 다음과 같다.
1. `strings`
2. `otool -ov`
3. `nm -u`

처음에는 `nm -u`를 사용해서 메소드를 리스팅했는데, 해당 `didReceiveMessage:`가 검출되지 않았다. 그래서, 클라이언트들과 적잖은 마찰?이 있었다.(그것도 추석연휴전에 들어온 문의...)
'우리 안사용하고 있는데? nm -u 로 확인해봤다구 !' 라면서....

추석연휴가 끝나고 재검사를 해보아도 마찬가지(추석연휴중에도 심볼에 대해서 공부를 해가며 검사를 했다는 건 슬픈 사실)

연휴가 끝난 오늘, `otool` 을 사용해서 검사를 해보니, 해당 메소드를 사용중인 파일이 오브젝트 파일로 linking이 되어있는 것을 발견하고, `헉`했지.
그리고 나서, `strings`로 다시 검사를 해보니, 모든 linking된, call이 되는 메소드들 리스트가 펼쳐지는데, 그 가운데 `didReceiveMessage:`가 있었다.

그래서, 결국은 메소드명을 바꾼후, 배포하기로 했다.


## 남은 사항들
조금은 이상하다. 수년전부터 사용중인(문제없이 !!) 라이브러리파일을 이제야와서 필터링한다는 것은,
Apple의 검수툴이 바뀌었거나, 검수 요건에 해당 메소드가 블랙리스트로 추가가 되었거나...
이상한건 `didClose:`, `didOpen`등과 같이 non-public API 처럼 보이는 많은 메소드들은 리젝사유가 아니라는 사실.
언젠가 얘네들도 리젝사유가 되면, 또 바꿔야할텐데...


이래서 네임스페이스가 필요하다고 생각하고...
이것은 여전히 objective-c 에서 불편한 사항인것 같다.


아무튼, 심볼에 대해서, 링킹에 대해서 그리고 검수에 대해서 조금은 발전할 수 있었던 '리젝'이었다.

끄읏