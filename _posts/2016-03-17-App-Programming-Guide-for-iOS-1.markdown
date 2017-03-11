---
layout: post
title:  "[Apple Dev]01.About iOS App Architecture"
date:   2016-03-17 09:00:00
categories: [Ios]
tags: [Translation,iOS,Application,Architecture,Apple]
---

# About iOS App Architecture

iOS 어플리케이션은 좋은 UX를 제공하는 것을 보장하여야 합니다. 단순하게 좋은 디자인이나 UI를 떠나서, 좋은 UX란 많은 것들을 포함하고 있습니다. 사용자들은 iOS 어플리케이션들이 최대한 적은 베터리를 소모하길 바라면서 빠르고 즉각 반응하기를 원합니다. 어플리케이션은 최신의 iOS 기기뿐만 아니라 기존의 기기들도 지원하여야합니다. 이러한 모든 스펙을 구현하는 것은 벅차보여지만, iOS는 여러분이 이것을 실행에 옮길 수 있도록 도와줍니다.

![](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/ios_pg_intro_2x.png)

이 문서는 iOS에서 당신의 어플리케이션이 잘 돌아가도록 만들어주는 핵심 기능들을 강조하고 있습니다. 당신은 이 문서에서 설명된 모든 기능들을 구현할 필요는 없지만, 이러한 기능들을 당신이 진행하는 프로젝트에서 고려할 필요가 있습니다.

## At a Glance
당신의 아이디어를 어플리케이션에 녹일 준비가 되었다면, 시스템과 당신의 어플리케이션 사이에 일어나는 interaction을 이해할 필요가 있습니다.

### Apps Are Expected to Support Key Features
시스템은 모든 애플리케이션들이 아이콘, 앱의 기능 정보와 같은 특정 리소스나 설정 데이터들을 가지고 있다고 여깁니다. Xcode는 모든 신규 프로젝트들에 대한 정보를 제공하지만 어플리케이션을 submit하기 전에 리소스 파일, 프로젝트의 정보들이 옳은 지를 확인해야합니다.
관련 링크 : [Expected App Behaviors](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/ExpectedAppBehaviors/ExpectedAppBehaviors.html#//apple_ref/doc/uid/TP40007072-CH3-SW2)


### Apps Follow Well-Defined Execution Paths
사용자가 어플리케이션을 시작한 순간부터, 종료하는 시간까지 어플리케이션은 잘 정의된 실행 경로를 따릅니다. 어플리케이션이 살아있는 동안, foreground와 background 사이를 왔다갔다 할 수 있으며, 종료되고 재실행될 수 있습니다. 또한, 잠시동안 sleep 모드에 들수도 있습니다. foreground에 있는 앱은 거의 모든 것을 할 수 있지만, background에 있는 앱은 할 수 있는 일이 거의 없습니다. 상태 변화를 이용해서 어플리케이션의 행동을 적절하게 조정할 수 있습니다.
관련 링크 : [Expected App Behaviors](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html#//apple_ref/doc/uid/TP40007072-CH2-SW1)

### Apps Must Run Efficiently in a Multitasking Environment
사용자에게 베터리 수명은 성능, 반응성, UX 와 같이 중요합니다. 어플리케이션의 베터리 사용량을 최소화하는 것은 장비를 재충전할 필요없이 앱을 하루 종일 켜놓을 수 있게합니다. 그러나 빠른 실행을 위해 준비되어 있는 것 또한 중요합니다. iOS 멀티태스킹 구현은 즉각적인 반응성과 사용자들이 원하는 UX를 희생시키는 것 없이 좋은 베터리 수명을 제공합니다. 그러나 그 구현은 시스템이 제공하는 기능들을 앱에서 채택하는 것을 필요로 합니다.

관련 링크 : [Background Execution](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html#//apple_ref/doc/uid/TP40007072-CH4-SW1)
[Strategies for Handling App State Transitions](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/StrategiesforHandlingAppStateTransitions/StrategiesforHandlingAppStateTransitions.html#//apple_ref/doc/uid/TP40007072-CH8-SW1)


### Communication Between Apps Follows Specific Pathways
보안을 위해서 iOS 앱은 샌드박스에서 실행됩니다. 그리고 다른 앱과의 interactions를 제한합니다. 시스템상의 다른 앱과 interaction을 원한다면, 해야할 특정한 일들이 있습니다.

[Inter-App Communication](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html#//apple_ref/doc/uid/TP40007072-CH6-SW2)

### Performance Tuning is Important for Apps
앱에 의해서 실행되는 모든 일들은 그것과 관련된 전원 소모를 가집니다. 유저의 베터리를 소모하는 앱들은 나쁜 UX를 제공하며, 한번 충전으로 하루종일 실행되는 다른 앱에 비해서 지워지기 쉽습니다. 시스템이 제공하는 전원 절약 방법과 다른 기능들간의 소모량을 알아놓으면 좋습니다.
[Performance Tips](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/PerformanceTips/PerformanceTips.html#//apple_ref/doc/uid/TP40007072-CH7-SW1)


## How to Use This Document
이 문서는 iOS 앱을 만들고자 하는 초보자용 문서가 아닙니다. 이 문서는 자신의 앱을 앱스토어에 올리기 전에 더욱 갈고 닦으려는 개발자들용입니다. 시스템과 당신의 앱 사이의 interaction을 이해하기 위해서, 그리고 그러한 interactions들이 부드럽게 일어날 수 있도록 이 문서를 사용하세요.  

## Prerequisites
이 문서는 iOS 앱 구조에 대해 구체적인 정보를 제공합니다. 그리고 많은 앱-level 특성들을 어떻게 구현하는 지를 보여줍니다. 이 문서는 당신이 이미 iOS SDK를 설치하였고, 개발 환경을 구성하였으며, Xcode상에서 앱을 만들고 구현하는 것에 대한 기초를 이해하고 있다고 가정하고 쓰여졌습니다.

만약 당신이 iOS 어플리케이션 개발에 처음이라면, "Start Developing iOS Apps Today" 문서를 읽으세요. 해당 문서는 개발 프로세스에 대한 순차적인 것을 제공하여, 당신에게 그것드릉ㄹ 빠르게 정리하여 알려줍니다. 또한 hands-on 튜토리얼을 포함하고 있어서 당신이 앱을 만드는 프로세스를 지나며 어떻게 간단한 앱을 만들고 실행시키는 지를 빠르게 보여줍니다.

## See Also
만약 당신이 iOS에 대해서 배우고 있다면 "iOS Technology Overview"를 읽고 기술들과 특징들을 당신의 iOS 앱에 포함시켜 보세요.

원문 : [링크](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072-CH1-SW1)