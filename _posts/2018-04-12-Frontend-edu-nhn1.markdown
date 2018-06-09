---
layout: post
title:  "[JavaScript] 웹프론트엔드 개발 개론1"
date:   2018-04-12 09:00:00
tags: javascript html5 frontent es6 es5
categories: devstory
---

이번 시간에는 회사 팀내 공유자료를 가져와봤습니다. 팀에서 진행했던 롤 및 도메인이 주로 모바일에 맞춰져있다보니,
웹에 대한 기초를 공유하는 자리가 필요했습니다. 그래서, 발표자료를 만들어서 공유를 하였는데요 ~
블로그에도 그대로 가져와봤습니다 :)

### 관련 포스트
- [[JavaScript] 웹프론트엔드 개발 개론1](/devstory/2018/04/12/Frontend-edu-nhn1/)
- [[JavaScript] 웹프론트엔드 개발 개론2](/devstory/2018/04/12/Frontend-edu-nhn2/)
- [강의자료 git 주소](https://github.com/MrKarl/lesson.javascript.frontend)


### TOC
1. HTTP 프로토콜 및 웹 기초 (0.5h)
2. HTML 문서 로딩 & 렌더링 (0.5h)
3. JavaScript 개론 (1h)
4. JavaScript 심화 (1h)
5. 실습 (2h)



### 시작전
- Online HTML code editor : 
    - https://jsfiddle.net/ 
    - http://cssdeck.com/labs


# 1. HTTP 프로토콜 및 웹 기초 (0.5h)
## HTTP?
Web & `Server-Client` 모델에서의 기본이 되는 프로토콜
HTML 문서 등 Web에 올라가는 `리소스 전송`에 해당되는 프로토콜


![frontend-dev-edu001](/static/assets/img/posts/frontend-lecture/frontend-dev-edu001.png)

> HTTP 프로토콜 버전에 따라서, HTML 1.0 과 HTML 1.1을 비교하여 이해하는 것이 중요하다.

### 특징
- `TCP` based
    - 리소스 전송에서의 유실을 최소화하기 위해 TCP를 주로 사용.
    - Google에서는 UDP 베이스의 HTTP 프로토콜을 연구/실험 중(실제로 검색엔진에 쓰이고도 있음)
- `Connectless`
    - requset수가 많을 때(warm connection), 3-way-handshake(TCP)의 오버헤드가 심하므로, Connection 헤더 등을 이용해 연결을 유지
    - Pipe Lining을 통해 하나의 TCP 연결을 효율적으로 사용함 (HTTP 1.1)
    - Keep Alive 기능 지원(HTTP 1.1)
    - HTML5 스펙으로 WebSocket이 있음. WebSocket 설정시, HTTP -> WebSocket 으로 프로토콜이 업그레이드됨.
- `Stateless`
    - HTTP에는 상태가 없다. == 1회 통신 완료 후, 연결을 끊어버리므로 클라이언트의 이전 상태를 알 수 없다.
    - HTTP Cookie를 이용한 세션 유지



#### 참고1
- Server : Tomcat, Resin, Apache, Nginx, IIS, NodeJS, Tmax Jeus, ...
    - [2017년 기준 웹서버 점유율](https://news.netcraft.com/archives/2017/11/21/november-2017-web-server-survey.html)
- Client : Web Browser, Web Robot, ...

#### 참고2
- 웹서버군에 위와 같은 서버들이 있는데, Web Server(WS)와 Web Application Server(WAS)는 분리하여 생각해야한다.
- WS 에는 apache, nginx 등이 있는데 이들은 HTTP Request를 받아서, 해당 Request에 부합하는 실제 정적 "리소스/응답"를 내려주는데 사용된다.
- WAS는 HTTP Request를 받아서, 해당 Request를 이용한 연산/DB 작업/파일 입출력 등등의 과정을 거친 후, 동적 "리소스/응답"을 내려주는데 사용된다. 즉, WAS는 기존 웹서버 기능에 "웹컨테이너" 기능이 합쳐진 서버를 의미하며, Tomcat, Resin, Jetty 등등이 있다.



## Web ?
HTTP 프로토콜은 애플리케이션 계층에 있다. 그렇기에, HTTP 헤더 등 확장가능한 부분이 많다. (TCP/IP는 전송 계층)
![frontend-dev-edu002](/static/assets/img/posts/frontend-lecture/frontend-dev-edu002.png)
[네트워크 계층도](http://hahahoho5915.tistory.com/15)



### TODO
- URI / URL
- HTTP Method
- HTTP Message Format
    - Request
    - Response



# 2. HTML 문서 로딩 & 렌더링 (0.5h)
## HTML 이란?
HTML(HyperText Markup Language)은 웹 페이지를 위한 마크업 언어이다. ~= XML

HTML은 2.0(실제로는 1990년을 전후하여 등장하였고, 표준 버전은 1995년 HTML 2.0으로 RFC 1866으로 발표됨)부터
많이 쓰고 있는 HTML 4.01 및 최근의 HTML 5로 발전해왔다.


### HTML Example
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>HTML5</title>
    <!-- link 태그는 head 안에서만 선언 할 수 있습니다. -->
    <link href="default.css" rel="stylesheet">
</head>
<body>
    <div id="myDiv">

        <!-- HTML 문서내 주석은 이렇게 달 수 있습니다. -->
        <!-- HTML 문서에서 <, > 문자는 태그를 감지하는 문자이므로, 해당 문자를 사용하고 싶을 경우에는 &lt; &gt; 를 사용해야합니다. 마찬가지로, 스페이스 또한 무시될 수 있으므로 꼭 필요한 경우 &nbsp; 로 치환하여 사용해야합니다.-->
        <h1>Hello, HTML5</h1>
        <p>This is Paragraph</p>
        <br/>
        <div style="width:200px; height:300px; background-color:black; color:white; font-size:20px;">
            Text
        </div>
        
        <hr/>
        
        <h1>HyperText Markup Language</h1>
        <div class="form">
            <input type="text" placeholder="test text" name="testText"/> <br/>

        </div>
        
        <div class="form">
            <input type="number" placeholder="1234567890" name="testNumber"/>
        </div>
    </div>
    
    <button id="btnApplyStyle">
        applyStyle
    </button>
</body>
</html>
```



```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
    <title>HTML4.01 Strict</title>
</head>
<body>
    <b>HTML 4.01 Strict 문서형식에서는 b 태그 사용을 허용하지 않습니다.<b>
    <i>HTML 4.01 Strict 문서형식에서는 i 태그 사용을 허용하지 않습니다.<i>
</body>
</html>
```








### HTML과 DOM
HTML은 웹페이지 외에도 **웹 문서**라고도 불리우는데, 이 문서는 **계층** 을 가지고 있다.
이 계층을 모델화 한 것이 **DOM**(Document Object Model)이고, 프로그래밍 인터페이스(규약)이다.

DOM 구현의 표준은 [W3C DOM](https://www.w3.org/DOM/), [WHATWG DOM](https://dom.spec.whatwg.org/) 등이 있으며, 필수적 명세 외에는 각기 브라우져에서 자체 구현하고 있으므로, 브라우져별로 그 기능이 상이할 수 있다.

`Web Page`(API) = `DOM` + `Script`(JavaScript/Python 등등) 

```python
# Python DOM example
import xml.dom.minidom as m

filePath = "C:\\Projects\\Py\\chap1.xml";
document = m.parse(filePath);
paragraphs = doc.getElementsByTagName("p");
```

```js
// JavaScript DOM example in WebBrowser

var paragraphs = document.getElementsByTagName('p');
```



### HTML 문서 렌더링
각 렌더링 엔진(WebKit-크롬 사파리, Mozilla Gecko-파이어폭스)마다 렌더링 과정상 다소 상이한 부분은 있지만, 큰 플로우는 비슷하다.
(참고 링크 : http://taligarsiel.com/Projects/howbrowserswork1.htm)

1. HTML 파싱
2. Render Tree 생성 (= DOM 트리 구축 + CSSOM 트리 구축) *(display:none 같은 경우 render tree에 들어가지 않음, 비 시각적 요소들은 제외됨)*
3. Reflow (= Layout, Render Node(= frame, box)들을 배치, 위치/크기 등을 계산)
4. Repaint (= Redraw, paint 및 composition을 통해 이미 계산된 위치에 스타일을 입힘.)

![frontend-dev-edu003](/static/assets/img/posts/frontend-lecture/frontend-dev-edu003.png)







# 3. JavaScript 개론 (1h)
### JavaScript ?
WebBrowser(넷스케이프)에서 동작하기 위해, 즉 웹페이지를 핸들링하기 위해 개발되었다.
하지만 이제는 WebBrowser 뿐만 아니라 Runtime(NodeJS(Chrome V8 JavaScript Engine Runtime), Apache CouchDB, Adobe Acrobat, MongoDB 등등)에도 올라가서 동작하고 있다.


### JavaScript 특징
- 가벼운 인터프리터 or JIT-Compile 형 언어(흔히 Script라 불리우는 언어의 특징)
- First Class Function을 가지고 있다.
    - Higher-Order Function 이 가능함.
        - JavaScript, C# !
        - Java는 Function이 First Class Object가 아님 !!! (물론 Kotlin은 First Class Function 지원)
- Prototype based Language
    - No class ! ES6 부터 class 키워드를 제공하고 있으나, class라는 것은 실제로 없고, syntax sugar일 뿐
    - 즉, "객체" 생성은 객체 원형인 프로타타입 객체를 복사(cloning)하여 생성하고, 확장함.


### JavaScript Version
`ES5`, `ES6` 두개만 기억합시다 !
- ES5
    - 현 대부분의 브라우져에서 지원하고 있는 스펙.
    - module 화가 지원이 안되기 때문에, amd/commonjs 등을 이용한 모듈화
    - `심화` 과정에서 모듈화를 다루도록 하겠음
- ES6
    - ES2015라고도 하며, class 키워드, module 지원 등등 현대적 스펙. 아직 미지원 브라우져가 많음. 
    - `심화` 과정에서 transpiling 을 다루도록 하겠음



### Prototype
1. Prototype
    ```js
    funciton Person() { }
    ```
    ![frontend-dev-edu004](/static/assets/img/posts/frontend-lecture/frontend-dev-edu004.png)
2. Instance
    ```javascript
    function Person(){ }

    var park = new Person();  
    var jeong = new Person();
    ```
    ![frontend-dev-edu005](/static/assets/img/posts/frontend-lecture/frontend-dev-edu005.png)
3. Prototype Chaning
    ```javascript
    function Person(name){
        this.name = name || 'No Name';
    }
    Person.prototype.getNumberOfEyes = function() {
        var numOfEyes = 2;
        return numOfEyes;
    };

    var park = new Person('Panki Park');
    var jeong = new Person();
    
    park.getNumberOfEyes = function() {
        var numOfEyes = 8;
        return numOfEyes;
    };
    
    jeong.age = 30;
    
    park.getNumberOfEyes(); // 8
    jeong.getNumberOfEyes(); // 2

    park.age; // undefined
    jeong.age; // 30
    
    park.name; // "Panki Park"
    jeong.name; // "No Name"
    
    
    
    /////  Prototype inheritance
    function Korean(name) {
        this.name = '이름 없음';
    }
    
    Korean.prototype = Person.prototype;
    
    var koreanPark = new Korean('박판기');
    koreanPark.name;
    koreanPark.getNumberOfEyes();
    ```
4. Prototype 심화
    ```js
    var Person = {
        name : 'No Name',
        getNumberOfEyes: function() {
            var numOfEyes = 2;
            return numOfEyes;
        }
    };
    
    var park = Object.create(Person);
    park.name; // No Name
    park.getNumberOfEyes(); // 2
    
    var jeong = Object.create(Person, {
        name: { value: 'Jeong'}
    });
    
    jeong.name; // "Jeong"
    jeong.getNumberOfEyes(); // 2
    ```



### DOM Handling
앞서 설명했듯, JavaScript는 웹문서(DOM)를 핸들링하기 위한 언어로 탄생되었다. 다음 예시들을 통해 DOM handling을 살펴보자.
html 은 윗부분(***HTML Example***)의 소스를 참조한다. (자세한 스타일 프로퍼티는 CSS 를 자가 학습하며 익히도록 한다.)
```js
// Style
function applyStyle() {
    // getElementById
  var myDiv = document.getElementById('myDiv');
  myDiv.style.border = '2px dashed #494949';
  myDiv.style.padding = '10px';

  // getElementsByTagName
  var h1s = document.getElementsByTagName('h1');
  h1s[0].style.color = "red";
  h1s[1].style.color = "gray";
}

// IIFE Pattern
(function() {
    var btnApplyStyle = document.getElementById('btnApplyStyle');
    btnApplyStyle.addEventListener("click", applyStyle);
})();

```





### Ajax
"Asynchronous JavaScript and XML"
AJAX is about updating parts of a web page, without reloading the whole page.

예전 인터넷 브라우저에는 Ajax 개념이 없었기 때문에, 변경사항을 위해 문서 통째로 요청/응답을 받을 수 밖에 없었다.
Ajax의 등장 이후로, 문서 일부분만을 갱신할 수 있게 되었고, 비동기 통신 처리가 가능해졌다.


```js
var method = 'GET';
var url = 'https://lnc.m.hangame.com:10443/hsp/lnc/getLaunchingInfos.json?gameNo=9104&gameClientVersion=1.0.0&platformSdkVersion=2.28&osType=2&osVersion=4.1.2&udid=d08dffedb847426942aa&deviceModel=SHW-M440S&market=KG&lcnt=1402975221717&language=ko&locale=KR&country=KR&mobileCountry=45005';

var xhr = new XMLHttpRequest();
xhr.open(method, url);

xhr.onload = function(e) {
    console.log(e);
    var myDiv = document.getElementById('myDiv');
    myDiv.innerHTML = JSON.stringify(e.currentTarget.response);
};

xhr.send();
```




### JavaScript Structure
- 자바스크립트는 단일 쓰레드 모델이다. ( O / X ) ? (<span style="color:white;"> O (조건부)</span>)
<span style="color:white;"> JavaScript Engine만이 단일 쓰레드이다. </span>
JavaScript는 JavaScript 엔진 뿐만이 아니라, Web APIs, Task Queue, Event Loop와 같은 다양한 영역으로 나눌 수 있다.

![frontend-dev-edu006](/static/assets/img/posts/frontend-lecture/frontend-dev-edu006.png)


다음 코드를 보며 원리를 이해해보자. (이해하려 노력해보자)



### JavaScript Scope && Context(Call Stack) && Closure
JavaScript의 Scope를 이해하려면 Context 및 Call Stack에 대한 이해가 필요하다. 또한, Closure 사용을 통해서 해당 이해를 높일 수 있다.

- JavaScript 의 Scope는 ~~Block~~이 아니라 ```````Function```````이다.

#### Example1
```js
var a = 1;
function Person() {
    this.say = "hello";
}

Person();
window.say; // "hello"

var person1 = new Person();
person1.say; // "hello"
```


#### Example2
```js
console.log('Global Context');

function f1() {
    console.log('f1 Context');
}

function f2() {
    f1();
    console.log('f2 Context');
}

f2();

/** Result
 *
 *  Global Context
 *  f1 Context
 *  f2 Context
 *
 */
```

#### Example3
```js
function outerFun(){  
    var temp = 0;
    return {
        innerFun1 : function() {
            temp += 1;
            console.log('temp :' + temp);
        },
        innerFun2 : function() {
            temp += 2;
            console.log('temp :' + temp);
        }
    };
}

var out = outerFun();
out.innerFun1(); // 1
out.innerFun2(); // 3
out.innerFun2(); // 5
out.innerFun1(); // 6 

var out2 = outerFun();
out2.innerFun1(); // 1
out2.innerFun2(); // 3
```


```js
var list = [];
for (var i=0; i<5; i++) {
    list[i] = function() {
        console.log('i: ' + i);
    };
    // list[i](); // 0, 1, 2, 3, 4
}

for (var j=0; j<5; j++) {
   list[j](); // 5, 5, 5, 5, 5
}

//////////////////////////////////
var list = [];
var closure = (i) => {
    list[i] = function() {
        console.log(i);
    }
};

for (var i=0; i<5; i++) {
    closure(i);
}

for (var j=0; j<5; j++) {
   list[j](); // 0, 1, 2, 3, 4
}
```