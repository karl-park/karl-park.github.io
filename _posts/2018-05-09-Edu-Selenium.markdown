---
layout: post
title:  "[Test] Selenium"
date:   2018-05-03 09:00:00
tags: java selenium testing test
categories: devstory
---
이번 시간에는 Selenium을 이용한 웹 UI 테스트를 진행해보도록 하겠습니다.

# Selenium
셀레니움은 웹브라우저 자동화 도구입니다. 엄밀하게는 "테스트" 도구가 아닌것이죠. 
또한, 유지보수 리소스가 많이 들기 때문에 프로젝트에 ㅅ니중히 선택해서 도입해야합니다.

현대 웹 서비스들은 JavaScript를 이용하여 UI를 핸들링하는 경우가 많으므로, JavaScript에 대한 이해가 필수적입니다.

한편, UI 테스트는 `Flaky Test` 라는 특성을 가지는데, 10번 성공하고 1번 실패하는 경우가 발생할 수 있습니다. 이럴 때, 확인하는 것이 극도로 어렵습니다.(`스크린샷`을 찍어서 저장하는 방법 등으로 대응가능하나, 정확한 원인을 찾기는 너무 어렵습니다)


## Selenium
Selenium은 앞서 설명했듯이 웹브라우저 자동화 도구입니다. 즉 브라우저를 컨트롤하는 API를 사용하여 자동화를 실행하게 됩니다.
웹 브라우저 테스트에 사용되는 자동화 프레임워크로써, 그 본질은 "테스트" 툴은 아닌 것이죠.

> Selenium is not a test tool


셀레니움의 구조는 다음과 같습니다.
### Selenium Structure
다양한 언어로 바인딩할 수 있으며, WebDriver를 통해 웹브라우저를 핸들링하게 됩니다.
![selenium001.png](/static/assets/img/posts/selenium/selenium001.png)


### Selenium Client
셀레니움 홈페이지에 들어가보면 아래와같이 다양한 클라이언트를 지원함을 알 수 있습니다.
![selenium002.png](/static/assets/img/posts/selenium/selenium002.png)



본문에서는 Java로 진행을 할 예정입니다. JavaScript 등의 클라이언트는 기회가 되면 써보도록 하겠습니다.


## WebDriver
셀레니움은 웹드라이버를 사용하여 웹브라우저를 핸들링하는데요, 아래 링크에서 보시는 것과 같이
셀레니움 내의 API를 사용하여 핸들링 할 수 있습니다.

[https://www.seleniumhq.org/docs/03_webdriver.jsp](https://www.seleniumhq.org/docs/03_webdriver.jsp)

## Selenium Test Step
1. Create a WebDriver instance
2. Native to a Web Page
3. Locate a HTML element on the Web Page.
4. Perform an action on an HTML element.
5. Anticipate the browser response to the action(Assertion).


## Selenium Grid
Selenium 테스트를 실행하는 Remote `서버`입니다. Local이 아니라 remote로 테스트를 하기 위해 사용됩니다.

- [직접 구축](https://github.com/groupon/Selenium-Grid-Extras)이나 상용 서비스도 있습니다.

## Selenium 의 한계
텍스트 비교 등을 위주로 합니다. UI상에서 비주얼 테스트를 하려면, 다소 복잡한 과정을 거쳐야합니다.
비주얼(레이아웃 등)은 자주 변하기도 하기에, 유지보수 비용이 너무 올라가는 부작용이 생길 수 있습니다.
그렇기에 레이아웃 테스트(Visual Testing) 등에는 한계가 있다고 여겨집니다.

최근에는 이런 Visual Testing을 지원하는 다른 "도구"들이 나오고 있습니다.

ex) 아래와 같은 이슈들은 테스트 사실상 어렵습니다.
- ![selenium003.png](/static/assets/img/posts/selenium/selenium003.png)
- ![selenium004.png](/static/assets/img/posts/selenium/selenium004.png)


## Selenide
Selenide는 Selenium의 사용성을 개선한 라이브러리입니다.

사실상 Selenium은 `테스트` 도구가 아니라, 웹브라우져 컨트롤러에 가깝기에, Selenide을 사용하여 셀레니움에 테스트기능을 더하여 사용합니다.
[http://selenide.org/](http://selenide.org/)




## 실습
- Source Path : `${project_root}/src/test/java/com.larzerycode/selenium/tests/sample/basic/GoogleExampleTest.java`

> 디폴트로 실행시키면, 북마크나 extension 등의 기능은 로드되지 않는다. 필요로 한다면, 코드상에 추가를 해야한다.


### 1. getDriver
```java
WebDriver driver = getDriver();
```

### 2. open url
```java
driver.get("http://www.google.com");
```


### 3. find element
```java
WebElement element = driver.findElement(By.name("q"));
```

- ![selenium005.png](/static/assets/img/posts/selenium/selenium005.png)



### 4. element.action()
```java
element.clear();
element.sendKeys("Cheese!");

// Now submit the form. WebDriver will find the form for us from the element
element.submit();
```

### 5. get data of element and verify
```java
assertTrue(driver.findElement(By.linkText("Cheese - Wikipedia")).isDisplayed());
```



## 유의할 점
  요새 많은 부분들은 ajax 통신으로, 웹 문서 로딩 이후에 비동기적으로 불러오기도 한다. 이 때, 만약 텍스트 비교를 "동기화"없이 하다보면, "비동기 프로세스"가 끝나기도 전에 테스트를 수행하여, 실패가 날 수도 있다.
  그렇기에,많은 경우에 `wait`을 이용하여, sync를 맞춰줘야한다.


단순 Test.java에 셀레니움 코드를 작성하지말고, `PageObject`를 항상 생성하여, 래퍼/헬퍼 형식으로 사용하도록 하자. 그래서 테스트 코드상에서는 직관적으로 무슨 동작을 하는지 알 수 있게하고, Page 클래스 내에서 셀레니움 API를 사용하면서 실제 동작을 수행하도록 하자.



## 테스트 권장 사항
- 테스트 병렬 실행
- 테스트 데이터는 고유하지 않는다.
    - unique 데이터를 사용하자.
    - 테스트 데이터 수명은 짧을 수록 좋다.
- 절대 ! Sleep을 사용하지 말자 !
    - WebDriverWait 등으로 블럭킹
    - 그리고 `Sleep` 만큼 테스트 시간이 늘어난다.
- REST API, Cookie, JavaScript 활용
- 페이지 디동 또는 page refresh 후에 주요 element를 확인하자
- 비동기 데이터 로드를 주의하자.
- coverage보다는 stability가 더 중요하다.


## 실습자료

```
# 필요도구: java8, maven3, chrome browser
git clone https://github.com/hyunil-shin/SeleniumTraining.git
cd SeleniumTraining/sample
./check.sh

# 결과
# - chrome 브라우져가 실행되고 테스트 2개가 수행된다.
```


## 느낀점
- 쉽다.
- 재밋다.
- 하지만, 복잡한 케이스 하려면, "유지보수" 측면에서 괴로울 것 같다.
- UI(Visual) Test 위주를 하려면 어떻게 해야할까? JavaScript를 잘 활용하면 되겠다.
- HTML5 canvas에서는 사용하기 힘들 것 같다. DOM 접근 자체가 제한되므로.
- HTML5 canvas에는 clicking wih coordinate 로 테스트가 필요한데, 내가 알기론 이게 보안상 안되는걸로....
    - 되는 방법이 있는지 찾아보자.
