---
layout: post
title:  "[iOS] CocoaPods 적용하기"
date:   2017-04-08 03:00:00
tags: objc ios cocoapods dependency
categories: devstory
---
CocoaPods

# CocoaPods 적용하기

## TOC
1. CocoaPods 분석
2. CocoaPods 특정 라이브러리 연동
3. CocoaPods에 라이브러리 배포
4. Appendix


# 1. CocoaPods 분석
## 0. What is CocoaPods
- URL : https://cocoapods.org

CocoaPods은 Ruby로 만들어진, 의존성 관리툴이다. (Mac이니 Ruby가 기본적으로 깔려있음.)

내부적으로 Ruby의 gem(Ruby version manager)을 이용하기 떄문에, gem이 필요하다.(gem을 이용하여, 의존성을 관리하는듯? 확실치않음)


> `CocoaPods` 는 Swift/Objective-C Cocoa 프로젝트를 위한 Library `dependency Manager` 이다.

> `Podfile` 이라는 파일안에서 `의존성이 관리`된다.


## 1. Install
- 환경 : Xcode 7 + 8

터미널에서 다음을 입력

```shell
$ sudo gem install cocoapods
```

문제있을시, 지웠다가 다시 !!
```shell
$ sudo gem uninstall cocoapods
$ sudo gem install cocoapods
```


## 2. CocoaPods 업데이트
업데이트를 위해서는 다음을 입력한다.(그냥 다시 설치하면 됨)

```shell
// 다시 설치 !
$ sudo gem install cocoapods

// Pre-Release 버전설치 !!
$ sudo gem install cocoapods --pre
```


* * *

# 2. CocoaPods 특정 라이브러리 연동
## 1. Get Started
`Podfile` 에서 Cocoa 프로젝트의 모든 의존성을 관리한다고 위에서 설명하였다. Cocoapods 도 설치했으니, 이제 의존성을 부여해보자.

특정 라이브러리를 붙이기 원하는 Cocoa 프로젝트 폴더로 가서, 다음 명령어를 입력해주자.

```ruby
$ vi Podfile

// Podfile
platform :ios, '8.0'
use_frameworks!

target '{MyProjectTargetName}' do
    pod 'AFNetworking', '~> 2.6'
    pod 'ORStackView', '~> 3.0'
end

> :wq

$ pod install
```

이렇게 하면, pods 들을 설치한다. 설치가 완료되면, 다음과 같이 ```workspace```를 열어주자. (xcproject를 열면 안되는 이유는 Pods 가 workspace 자체를 생성하여, 디펜던시를 걸기 때문이다)

### Workspace 가 이미 존재하고 있을 때
만약 !! workspace가 존재 한다면, 다음의 구문을 "추가"해주자.

```ruby
workspace '{MyOwnWorkSpace}'
```



## 2. `pod install` vs. `pod update`
차이를 명확하게 두자 !

|pod install | pod update |
|-|-|
| 새로운 pods 설치시 | 기존 설치된 pods를 최신으로 갱신시 |
| 즉, 이미 pod install을 했더라도, 새로운 pods 인스톨을 위해서는 사용해야함 | 새로운 pods는 인스톨이 안됨 |




# 3. CocoaPods에 라이브러리 배포
`내용 추가 예정`

## 1. Pod Lib Create
```ruby
$ pod lib create 'pod name'
> 이후에 나오는 여러 질문에 응답 !
```

***pod-template***을 사용할 수도 있다.
```ruby
$ pod lib create 'pod name' --template-url=URL
```

참고 : [pod-template](https://github.com/cocoapods/pod-template)

그 후, `pod install`을 하여, Project를 하여 Project를 초기화 !
다음과 같은 폴더구조를 가진다.
```
$ tree MyLib -L 2

  MyLib
  ├── .travis.yml
  ├── _Pods.xcproject
  ├── Example
  │   ├── MyLib
  │   ├── MyLib.xcodeproj
  │   ├── MyLib.xcworkspace
  │   ├── Podfile
  │   ├── Podfile.lock
  │   ├── Pods
  │   └── Tests
  ├── LICENSE
  ├── MyLib.podspec
  ├── Pod
  │   ├── Assets
  │   └── Classes
  │     └── RemoveMe.[swift/m]
  └── README.md
```


각 파일/폴더 역할 !!

| File | |
| - | - |
|.travis.yml | a setup file for travis-ci |
| _Pods.xcproject | a symlink to your Pod's project for Carthage support |
| LICENSE | defaulting to the MIT License | 
| MyLib.podspec | the Podspec for your Library |
| README.md | a default README in markdown |
| RemoveMe.swift/m | a single file to to ensure compilation works initially | 
| Pod |  This is where you place your library's classes
| Example | This is the generated Demo & Testing bundle |
  

## 2. Get Started
**개발환경**
```
pod 'Name', :path => '~/code/Pods/'

pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :branch => 'dev'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :tag => '3.1.1'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :commit => '0f506b1c45'
```

`.podspec`, `LICENSE` File !!



### 1. Public Repo
CocoaPods 의 Specs Repo에 모든 libraries 들이 올라간다.
[CocoaPods Specs git Repository](https://github.com/CocoaPods/Specs)



### 2. Private Repo
https://guides.cocoapods.org/making/private-cocoapods.html

1) **My Own Spec Repo** 를 만들자 !
    누구나가 접근가능한 Repo를 선택하자.
    CocoaPods/Spect Master Repo를 fork/push 할 필요가 없다.
    git에 repo 를 만들자.
2) CocoaPods 에 **Private Repo를 추가**하자.
```bash
$ pod repo add REPO_NAME SOURCE_URL
$ cd ~/.cocoapods/repos/REPO_NAME
$ pod repo lint .
```
3) **Podspec 파일**을 Repo에 추가하자.
아래 명령은 `pod spec lint`를 실행한다.
```bash
$ pod repo push REPO_NAME SPEC_NAME.podspec
```
4) Podfile에서 private Pod를 사용하자 ~
```bash
source 'URL_TO_REPOSITORY'
```
#### Example

```ruby
// Create a Private Spec Repo
$ cd /opt/git
$ mkdir Specs.git
$ cd Specs.git
$ git init --bare



// Add your repo to your CocoaPods installation
$ pod repo add artsy-specs git@github:artsy/Specs.git
$ cd ~/.cocoapods/repos/artsy-specs
$ pod repo lint .
// (The rest of this example uses the repo at https://github.com/artsy/Specs)


// Add your Podspec to your repo
$ cd ~/Desktop
$ touch Artsy+OSSUIFonts.podspec


>
Pod::Spec.new do |s|
  s.name             = "Artsy+OSSUIFonts"
  s.version          = "1.1.1"
  s.summary          = "The open source fonts for Artsy apps + UIFont categories."
  s.homepage         = "https://github.com/artsy/Artsy-OSSUIFonts"
  s.license          = 'Code is MIT, then custom font licenses.'
  s.author           = { "Orta" => "orta.therox@gmail.com" }
  s.source           = { :git => "https://github.com/artsy/Artsy-OSSUIFonts.git", :tag => s.version }
  s.social_media_url = 'https://twitter.com/artsy'

  s.platform     = :ios, '7.0'
  s.requires_arc = true

  s.source_files = 'Pod/Classes'
  s.resources = 'Pod/Assets/*'

  s.frameworks = 'UIKit', 'CoreText'
  s.module_name = 'Artsy_UIFonts'
end


> :wq


// Save podspec and add to the repo
$ pod repo push artsy-specs ~/Desktop/Artsy+OSSUIFonts.podspec







```





### 3. Project Structure 변경
<??>



## 4. Appendix
### 1. Pods 를 source control에 추가해야하나요?

| 추가 장점 | 추가 안할때의 장점 |
| - | - |
| 1. Repo를 Cloning받자마자, **pod install** 등의 작업없이, 바로 빌드를 해볼 수 있다 !<br/>2. 인터넷이 끊겼을 때나, Pod source(github) 등이 다운되었을 때에도, 사용가능하다.| 1. Source control repo 크기가 작아진다. <br/> 2. 다른 Pod version 등으로 머지를 했을 때, 충돌이 없으니 다루기 쉽다. <br/> 2. Pods Repo가 존재하는 한, 동일한 버전의 Pods를 누구나 공유할 수 있다. |


### 2. Podfile.lock ?
`Podfile.lock` 파일은 `pod install` 실행 후에, 바로 생성이 됩니다. 이 파일은 설치된 각 Pods들의 버전을 추적합니다.



### 3. Podfile
`Podfile` 은 한개 이상의 Cocoa Projects 들에 대한 의존성을 말해주는 명세서라고 생각하면 됩니다.


#### Examples
**Single Target**
```ruby
target 'MyApp' do
    use_frameworks!
    pod 'Alamofire', '~> 3.0'
end
```

**More Complex Podfile (linking an `App` & its `test bundle`)**
```ruby
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/Artsy/Specs.git'

platform :ios, '9.0'
inhibit_all_warnings!

target 'MyApp' do
  pod 'GoogleAnalytics', '~> 3.1'

  # Has its own copy of OCMock
  # and has access to GoogleAnalytics via the app
  # that hosts the test target

  target 'MyAppTests' do
    inherit! :search_paths
    pod 'OCMock', '~> 2.0.1'
  end
end

post_install do |installer|
  installer.pods_project.targets.each do |target|
    puts target.name
  end
end
```


**여러개의 타겟이 같은 Pods를 공유할 때** --> `absract_target` 사용 !
```ruby
# There are no targets called "Shows" in any Xcode projects
abstract_target 'Shows' do
  pod 'ShowsKit'
  pod 'Fabric'

  # Has its own copy of ShowsKit + ShowWebAuth
  target 'ShowsiOS' do
    pod 'ShowWebAuth'
  end

  # Has its own copy of ShowsKit + ShowTVAuth
  target 'ShowsTV' do
    pod 'ShowTVAuth'
  end
end


// 아래와 같이 쓸 수도 있음 !!!!!!!!!!!!!!!
pod 'ShowsKit'
pod 'Fabric'

# Has its own copy of ShowsKit + ShowWebAuth
target 'ShowsiOS' do
  pod 'ShowWebAuth'
end

# Has its own copy of ShowsKit + ShowTVAuth
target 'ShowsTV' do
  pod 'ShowTVAuth'
end
```




#### Pod Version 지정하기
```ruby
pod 'Objection', '0.9'
```
Podfile 에서 위와 같이 버전을 지정하게 되는데, 여러 operator들이 있다. 한번 살펴보자 !

|Operator|Meaning|
| - | - |
| > 0.1 | 0.1보다 큰 Any version |
| >= 0.1 | 0.1보다 크거나 같은 Any version |
| < 0.1 | 0.1보다 작은 Any version |
| <= 0.1 | 0.1보다 작거나 같은 Any version |
| ~> 0.1.2 | 0.1.2 혹은 0.2 미만의 버전 |
| ~> 0.1 | 0.1 혹은 1.0 미만의 버전 |
| ~> 0 | 0 보다 큰 버전, (생략이 default) |

(참고 : 루비 [semantic-versioning](http://guides.rubygems.org/patterns/#semantic-versioning))


### 4. Spec 파일 톺아보기
Podspec 혹은 Spec은 Pod library의 버전을 설명합니다.


#### Spec 파일 생성
`pod spec create` 를 통해 spec파일을 생성하거나 직접 작성할 수 있습니다.

**spec 파일 예시***
```ruby
Pod::Spec.new do |spec|
  spec.name         = 'libPusher'
  spec.version      = '1.3'
  spec.license      = 'MIT'
  spec.summary      = 'An Objective-C client for the Pusher.com service'
  spec.homepage     = 'https://github.com/lukeredpath/libPusher'
  spec.author       = 'Luke Redpath'
  spec.source       = { :git => 'git://github.com/lukeredpath/libPusher.git', :tag => 'v1.3' }
  spec.source_files = 'Library/*'
  spec.requires_arc = true
  spec.dependency 'SocketRocket'
end
```


> `참고`
> Specs Repo(git) 에는 사용가능한 모든 Pods 들의 목록이 있다.
> https://github.com/CocoaPods/Specs




#### Subspec 파일 만들기
Subspec을 사용하면, Podspec 기능을 잘라서, 사람들이 library의 subspec을 사용할 수 있도록 해준다.

```ruby
Pod::Spec.new do |spec|
  spec.name          = 'ShareKit'
  spec.source_files  = 'Classes/ShareKit/{Configuration,Core,Customize UI,UI}/**/*.{h,m,c}'
  # ...

  spec.subspec 'Evernote' do |evernote|
    evernote.source_files = 'Classes/ShareKit/Sharers/Services/Evernote/**/*.{h,m}'
  end

  spec.subspec 'Facebook' do |facebook|
    facebook.source_files   = 'Classes/ShareKit/Sharers/Services/Facebook/**/*.{h,m}'
    facebook.compiler_flags = '-Wno-incomplete-implementation -Wno-missing-prototypes'
    facebook.dependency 'Facebook-iOS-SDK'
  end
  # ...
end
```





#### 배포/제출전 확인하기
1. `pod spec lint` : error/warning 이 없도록 !
2. 라이브러리 업데이트 ! with Semantic Versioning
3. 업데이트 내용이 이전 설치에 영향이 있었는지 확인
4. 테스트





# 참고 URL
- Podfile Syntax : http://guides.cocoapods.org/syntax/podfile.html
- Podspec Syntax : http://guides.cocoapods.org/syntax/podspec.html






CocoaPods 를 적용할 때,
vendor_frameworks 등을 이용해서,
소스코드는 숨긴채, 배포를 해아한다.

즉, 이렇게되면, 보다 간단하게 저장소등을 구축할 수 있다 !!!
-> https://stackoverflow.com/questions/34609087/non-open-source-cocoapods
-> https://www.google.co.kr/search?q=cocoapod+without+source+code&oq=cocoapod+without+source+code&aqs=chrome..69i57j69i64j69i60.6542j0j1&sourceid=chrome&ie=UTF-8



# CocoaPods Packager 
- 질문글 : https://github.com/CocoaPods/swift/issues/26
    - 답변 : https://github.com/CocoaPods/cocoapods-packager
- 오오오 !

```shell
$ pod package --help
>. 
Usage:

    $ pod package NAME [SOURCE]
    ......
```