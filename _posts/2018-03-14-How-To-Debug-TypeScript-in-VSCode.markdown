---
layout: post
title:  "[TypeScript] How to debug TypeScript in VS Code"
date:   2018-03-14 14:30:00
tags: javascript typescript debug vscode
categories: devstory
---
TypeScript로 작성한 웹앱 및 라이브러리는 결국 JavaScript로 컴파일되어 웹페이지에 로딩되기 때문에 디버깅을 할 경우, JavaScript 파일 디버그로 제한되어 original source(TypeScript)의 코드를 확인하기 어렵다.

이에, 본문에서는 TypeScript Debug 방법을 알아보고자 한다.

> 참고 : 해당 본문은 VSCode를 기준으로 작성되었다. 다른 에디터의 경우 비슷한 기능들을 각기 플러그인 형태 등으로 제공하고 있으므로 참고하여 진행하면 된다.


# TypeScript Debugging w/ SourceMap
``````SourceMap``````은 마치 iOS의 debugSymbol 과 같은 느낌이라고 생각하면 된다. 즉, sourceMap 파일은 TypeScript코드가 JavaScript로 컴파일 되는 과정에서 심볼들의 상관관계를 가지고 있는 json파일이다.

## 1. 웹서버 이용하여 디버깅 하는 방법

### Generate source map files
TypeScript의 Compile Option 내용을 포함한 tsconfig.json 파일에 아래와 같은 옵션을 추가하면 된다.

```json
{
    "compilerOptions": {
        "module": "system",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "outFile": "built/tsc.js",
        "sourceMap": true
    },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```


### Debugger for Chrome
VSCode 상에서 TypeScript등을 디버깅하기 위해서는 확장 프로그램인 `Debugger for Chrome`가 필요하다. 다음과 같이 설치해준다.

![Inline-image-2018-03-27 14.35.21.493.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_1.png)




### Launch WebServer
웹페이지에서 로드를 해야하기 때문에, 웹페이지를 올릴 수 있는 웹서버를 실행해주어야한다. 해당 본문에서는 VSCode 플러그인 중 하나인 [Live Server](https://github.com/ritwickdey/live-server-web-extension)를 이용하여 진행하도록 하겠다.

Live Server는 VSCode의 마켓플레이스에서 검색하여 설치할 수 있다.
![Inline-image-2018-03-27 14.31.14.267.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_2.png)

설치후 실행은 아래 그림과 같이 VSCode의 하단, `Go Live` 버튼을 클릭해주면 된다.
![Inline-image-2018-03-27 14.30.24.093.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_3.png)


### Build
작업한 TypeScript 파일을 웹에서 동작하게 하기 위해서는 JavaScript로의 빌드가 되어야한다.
해당 빌드는 다음의 명령어를 통해 할 수 있다.

```shell
$ tsc
```

위 명령어를 프로젝트 root에서 실행하게 되면, tsconfig.json에 따라서, 컴파일을 진행하게 되고, 설정에 따라서 `built/tsc.js`로 빌드가 된다. 또한 sourceMap 설정을 하였으므로, `built/tsc.js.map` 파일도 생성되게 된다. 


- 자동 TypeScript 빌드
만약 TypeScript 소스코드 변경에 따라서, 자동으로 빌드되게 하려면 VSCode의 `빌드`의 `tsc: 보기`를 선택하면, source file 변경시마다, 알아서 `tsc` 빌드를 수행해준다.
    - ![Inline-image-2018-03-27 17.43.59.003.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_4.png)


### Debugging
디버깅을 위해서는 디버깅하길 원하는 위치에 break point를 걸고나서 다음과 같이 "디버깅을 시도"해준다.
그 전에 `Live Server`를 통해 웹서버를 올려주어야한다.

1. Build 및 launch.json 파일 생성
    - ![Inline-image-2018-03-27 17.47.57.209.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_5.png)
2. launch.json 파일에 chrome launch 설정
    - `Live Server`가 5500 포트에서 실행되고 있으므로, 해당 포트로 변경해준다. (디폴트는 8080포트로 생성됨)
    - ![Inline-image-2018-03-27 17.52.02.062.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_6.png)
3. Break 설정 및 실행
    - 중단점을 설정해주고, 디버그를 실행해준다.
    - ![Inline-image-2018-03-27 17.53.44.684.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_7.png)
4. 확인
    - 실행을 하게 되면, 크롬이 열리면서, 자체 브레이크 포인트 등에서 멈추고, 아래처럼 변수등을 볼 수 있다.
    - ![Inline-image-2018-03-27 17.55.08.045.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_8.png)





## 2. nodeJS 이용하여 디버깅 하는 방법
### launch.json 설정 추가
![Inline-image-2018-03-27 18.07.46.170.png](/static/assets/img/posts/javascript/how2debugTS/how2debugTS_9.png)


### debugging
위의 스크린샷에서 debug 버튼을 눌러서 디버깅을 하면 된다.
이 때, TypeScript를 디버깅하기 위해서는 sourceMap 파일이 같은 폴더내에 위치해야한다.


## Reference
- [Debug TypeScript w/ Visual Studio Code - Youtube](https://www.youtube.com/watch?v=H1lgYojMCaQ)
- [Debugging TypeScript - WebStorm](https://www.jetbrains.com/help/webstorm/debugging-typescript.html)
- [ts-node : TypeScript Debugging w/o Compile](https://github.com/EnterpriseJSTutorial/vscode-ts-node-debugging)