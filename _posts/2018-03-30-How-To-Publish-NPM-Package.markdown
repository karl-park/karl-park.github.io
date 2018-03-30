---
layout: post
title:  "How to Publish a npm Package"
date:   2018-03-30 09:00:00
categories: [Javascript]
tags: [NodeJS,JavaScript,npm,Publishing,Package]
---

본 티켓에서는 `npm`에 본인이 만든 `package를 어떻게 배포`하는지에 대해서 다룹니다.

# TOC
I. Publish Package
II. Update Package(Versioning)

# I. Publish a Package

## Prerequisite
1. npm policy 숙지
    - npm에 패키지를 배포하기 위해서는 site 에티켓, 네이밍룰, 라이센스 등 가이드라인을 준수해야합니다.
    - 자세한 사항은 링크를 참조하세요. [링크](https://www.npmjs.com/policies)
2. npm 계정 생성, 로그인, 확인
    - `$ npm adduser`
    - `$ npm login`
    - `$ npm whoami`
    - url address : `https://www.npmjs.com/~${username}`
        - ex) https://www.npmjs.com/~karlpark


## 배포 준비
1. package.json 파일 확인
    - package.json 파일에 해당 패키지에 대한 정보가 담겨있습니다. 해당 내용을 꼼꼼히 작성합니다.
    - unique한 이름을 선택합니다. (scope 사용시 unique할 필요는 없습니다. [scope](https://docs.npmjs.com/misc/scope))
    - 자세한 사항은 링크를 참조합니다. [링크](https://docs.npmjs.com/getting-started/using-a-package.json)
2. README.md 파일 작성
    - documentation 파일을 작성합니다.

## Publish
1. publish & test
    - `$ npm publish`
    - url address : `https://npmjs.com/package/${package}`



# II. Update a Package(Versioning)
- https://docs.npmjs.com/getting-started/publishing-npm-packages#how-to-update-a-package


## Update a Version
1. Package Version Update
    - `$ npm version ${version_type}`
    - version_type 은 [semantic versioning](https://docs.npmjs.com/getting-started/semantic-versioning)을 따릅니다.
    - 위 명령어는 package.json의 version을 수정해줍니다.
2. README.md 파일 수정


## Publish a Package
1. 다음 명령어를 통해 Version을 Update  해줍니다.
    - `$ npm publish`
2 확인
    - url address : `https://npmjs.com/package/${package}`



# Appendix
## git을 이용한 template 세팅
다음 명령어를 통해서, github에 올려둔 템플릿 소스를 받는 방법도 있습니다.(nodejs/npm/git 설치 필요)

아래와 같이 git clone을 하는 과정 및 템플릿을 고를 수 있는 것을 package로 제작할 수도 있습니다.

### phaser official template (Webpack, JavaScript)
``` shell
$ git clone https://github.com/photonstorm/phaser-ce
$ cd "phaser-ce/resources/Project%20Templates/Webpack"
$ npm install
```

### Custom template(TypeScript)
``` shell
$ git clone https://github.com/MrKarl/phaser-tutorial.git
$ cd phaser-tutorial
$ npm install
```