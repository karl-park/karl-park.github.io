---
layout: post
title:  "[JavaScript] 웹프론트엔드 개발 개론2"
date:   2018-04-12 13:00:00
tags: javascript html5 frontent es6 es5
categories: devstory
---

지난 시간의 [[JavaScript] 웹프론트엔드 개발 개론1](/devstory/2018/04/12/Frontend-edu-nhn1/) 포스팅에 이어서, 2부를 연재하도록 하겠습니다.


### 관련 포스트
- [[JavaScript] 웹프론트엔드 개발 개론1](/devstory/2018/04/12/Frontend-edu-nhn1/)
- [[JavaScript] 웹프론트엔드 개발 개론2](/devstory/2018/04/12/Frontend-edu-nhn2/)


# 4. JavaScript 심화 (1h)
이번 시간에는 JavaScript의 모듈화를 학습하고, 적절하게 ```````bundling```````하고, ``````transpiling`````` 하는 법을 배워본다.
이러한 일들은 모두 ```npm```을 이용하여 진행할 예정이다.

(TypeScript에 대한 강의내용은 없다. 스스로 학습을 해야한다.)


## 시작하기 전에..

### IIFE
저번시간에 다룬 IIFE의 특징을 "호이스팅"이라는 JavaScript 특징을 결합해서 생각해보자.

- Hoisting
    - Example1 (Variable Hoisting)
    ```js
    // Before
    console.log(myName); // undefined;
    var myName = 'Panki Park';
    
    // After
    var myName;
    console.log(myName);
    myName = 'Panki Park';
    ```
    - Example2 (Function Hoisting)
    ```js
    // Before
    console.log(hello()); // hello;
    function hello() {
        return 'hello';
    }

    // After
    funtion hello() {
        return 'hello';
    }

    ```
    - Example3 (It is not Function Hoisting)
    ```js
    // Before
    console.log(sayBye()); // sayBye is not a function
    var sayBye = function() {
        return 'bye';
    }
    
    // After
    var sayBye;
    console.log(sayBye()); // sayBye is not a function
    sayBye = function() {
        return 'bye';
    }
    ```
    - Example4 (It is not Function Hoisting)
    ```js
    // Before
    console.log(sayBye()); // sayBye is not a function
    var sayBye = new Function('', 'return "bye"');
    
    // After
    var sayBye;
    console.log(sayBye()); // sayBye is not a function
    sayBye = new Function('', 'return "bye"');
    ```


### 모듈
JavaScript의 Scope관리는 지난 시간에서 본 것과 같이, `매우 지저분`하다.
예를 들어서, 다음의 두 파일은 서로의 변수를 공유한다. 모든 변수가 window 객체(전역) 스코프 위에 생성이 되기 때문이다.

- example 1
    ```js
    // scope1.js
    'use strict';
    var a = 'hello';
    var c = 'test';
    ```


    ```js
    // scope2.js
    'use strict';
    var b = a + ' my friends';
    var c = 'it is changed';
    alert(b);
    alert(c);
    ```
    
아래 예시는 nameSpace 패턴을 이용하여 변수를 공유한다.
- example 2
    ```js
    // scope1.js
    'use strict';
    var myNameSpace = (function() {
        var a = 'hello';
        var c = 'test';
        
        var say = function() {
            console.log(a);
        };
        
        return {
            a: a,
            say: say
        };
    })();
    ```

    ```js
    // scope2.js
    'use strict';
    var b = myNameSpace.a;
    alert(b);
    alert(c); // undefined (Uncaught ReferenceError)
    ```

위와 같이 "namespace" 패턴을 이용하여 스코프를 제한하였었는데, 이를 근본적으로 해결하기 위해
es6의 module(import 구문)이 나왔다. (그 이전에는 module.exports-require(commonjs-nodejs), define-require(amd, RequireJS-browser) 등을 이용하여 모듈화를 하였다.)


- ES6 import Supports : [Mozilla Link](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import#AutoCompatibilityTable)




## JavaScript 배포에서 2가지 기억할 것
1. `Transpiling`
2. `Bundling`



### Transpiling
- ES6/ES7 코드를 ES5 코드로 변환하는 것을 Transpiling이라고 한다. (사실 `정확`하게는 TypeScript도 컴파일이 아니라 Transpiling된다고 표현한다.)
- 대표적인 도구 : **Babel**, Traceur, ...


### Bundling
- 다수의 모듈들을 한개 이상의 파일로 합쳐주는 기능을 Bundling이라고 한다. 이러한 점에서, js 뿐만 아니라, css 등도 하나의 파일로 합쳐줄 수 있다.
- 대표적인 도구 : **Webpack**, Browserify








# 5. 실습 (2h)

## 준비
개발툴은 Visual Studio Code로 진행한다.

### Visual Studio Code 설치
- https://code.visualstudio.com/

### npm 설치
- https://nodejs.org/

### npm 설정
- package.json

### 기타 환경설정
- webpack 4


## I. 기본 페이지 제작 및 웹페이지 로딩
### 1. "npm" 프로젝트 생성/설정
- 프로젝트 폴더 생성
- npm init
    - package.json 설정은 추후.
- 프로젝트 폴더 구조 생성
    ```
    public/assets/js
    public/assets/css
    public/assets/img
    public/libs/
    src/
    ```


### 2. 기초 파일들 생성
- html (required)
- css (optional)
- js (required)


### 3. Run on Server
- Live Server 설치
    - [WIKI 링크 참고](http://wikin.nhnent.com/display/gp/01.+Visual+Studio+Code)

- nodejs 서버 run
...





## II. Bundling / Transpiling
### Webpack 설치
```shell
$ npm install webpack webpack-cli --save-dev
```

### Babel 설치
```shell
$ npm install babel babel-cli --save-dev
```







## II. JavaScript 모듈 제작
### 1st Iteration
- 요구사항
    - message, okCallback, cancelCallback 파라미터 3개를 받아서, message를 띄워주고 응답결과에 따라 ok,cancel callbacak을 내려줄 수 있는 모듈 개발 <span style="color:white;">confirm(message) : boolean</span>
        - askMessage(message, okCallback, cancelCallback);
    - 특정 DOM의 id 값을 받아서, 해당 DOM의 스타일을 세팅해주는 모듈 개발 <span style="color:white;">element.style.border</span>
        - makeBorderStyle(id, strokeWidth, color);
        - ***border : "10px solid #000000"***
    - 특정 DOM의 id 값을 받아서, 해당 DOM의 내용을 채워주는 모듈 개발 <span style="color:white;">element.innerHTML</span>
        - printMessgae(id, message);
    - 비동기 통신 모듈을 만들어서, 특정 DOM id 내부에 그 결과값을 뿌려줌. <span style="color:white;">XMLHttpRequest()</span>
        - requestAndPrint(id, url, callback); <span style="color:white;">xhr.responseText</span>
            - url : *`https://lnc.m.hangame.com:10443/hsp/lnc/getLaunchingInfos.json?gameNo=9104&gameClientVersion=1.0.0&platformSdkVersion=2.28&osType=2&osVersion=4.1.2&udid=d08dffedb847426942aa&deviceModel=SHW-M440S&market=KG&lcnt=1402975221717&language=ko&locale=KR&country=KR&mobileCountry=45005`*
            - method : *`GET`*

### 2nd Iteration
- 요구사항
    - 각 모듈을 모두 사용하는 app.js 개발
    - 전역에서 로딩된 모듈을 호출하여 다음과 같은 화면을 만드는 것이 목표
    - ![frontend-dev-edu007](/static/assets/img/posts/frontend-lecture/selenium007.png)
    - ![frontend-dev-edu008](/static/assets/img/posts/frontend-lecture/selenium008.png)
    - ![frontend-dev-edu009](/static/assets/img/posts/frontend-lecture/selenium009.png)
    
### 3rd Iteration
- 각 모듈들을 bundling 할 것 (webpack 이용)

### 4th Iteration
- 외부 라이브러리 사용



```js
// webpack.config.js
module.exports = {
    entry: './public/assets/js/app.js',
    mode: 'development',
    output: {
      path: __dirname,
      filename: './public/assets/js/bundle.js'
    },
}
```



## SourceMap in Production
- SourceMap을 이용한 debugging 등을 설명합니다.
    - https://blog.sentry.io/2015/10/29/debuggable-javascript-with-source-maps
- 보안상 노출되지 말아야한다면, Production 환경에 SourceMap 배포는 하지 말아야합니다.
    - 교육 시간에 논의했던대로, 별도의 개발 환경을 구축하여, source map 배포 후 테스트하는 것은 가능하리라 봅니다.
-  만약, 노출되어도 된다면, 항상 source map 배포를 하는 것도 좋다고 여겨집니다. 
    - 실 사용자에게는 영향이 없고, 개발자 도구를 열어서 작업을 하는 사용자에게만 네트워크 비용등이 발생합니다.



## JavaScript Apply, bind, Call
- `this`를 바꿔주는 메소드들
    - apply, bind, call 모두 this를 첫번째 파라미터로 바꿔줍니다.
    - apply, call은 적용 후, 실행을 해줍니다.
    - bind는 적용만 하고 실행은 하지 않습니다.
```js
var panki = {
    name : 'panki park',
    say: function() {
        alert('hello, ' + this.name);
    }
};

var developer = {
    name : '개발자'
};

panki.say(); // 'hello, panki park';
panki.say.call(developer); // 'hello, 개발자'
```

