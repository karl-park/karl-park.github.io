---
layout: post
title:  "01. About iOS App Architecture"
date:   2016-03-17 12:00:00
tag:
- iOS
- Translation
- Application
- Apple
technology: true
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
Battery life is important for users, as is performance, responsiveness, and a great user experience. Minimizing your app’s usage of the battery ensures that the user can run your app all day without having to recharge the device, but launching and being ready to run quickly are also important. The iOS multitasking implementation offers good battery life without sacrificing the responsiveness and user experience that users expect, but the implementation requires apps to adopt system-provided behaviors.

Relevant Chapters: Background Execution, Strategies for Handling App State Transitions
Communication Between Apps Follows Specific Pathways
For security, iOS apps run in a sandbox and have limited interactions with other apps. When you want to communicate with other apps on the system, there are specific ways to do so.

Relevant Chapter: Inter-App Communication
Performance Tuning is Important for Apps
Every task performed by an app has a power cost associated with it. Apps that drain the user’s battery create a negative user experience and are more likely to be deleted than those that appear to run for days on a single charge. So be aware of the cost of different operations and take advantage of power-saving measures offered by the system.

Relevant Chapter: Performance Tips
How to Use This Document

This document is not a beginner’s guide to creating iOS apps. It is for developers who are ready to polish their app before putting it in the App Store. Use this document as a guide to understanding how your app interacts with the system and what it must do to make those interactions happen smoothly.

Prerequisites

This document provides detailed information about iOS app architecture and shows you how to implement many app-level features. This book assumes that you have already installed the iOS SDK, configured your development environment, and understand the basics of creating and implementing an app in Xcode.

If you are new to iOS app development, read Start Developing iOS Apps Today. That document offers a step-by-step introduction to the development process to help you get up to speed quickly. It also includes a hands-on tutorial that walks you through the app-creation process from start to finish, showing you how to create a simple app and get it running quickly.

See Also

If you are learning about iOS, read iOS Technology Overview to learn about the technologies and features you can incorporate into your iOS apps.




원문 : https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072-CH1-SW1