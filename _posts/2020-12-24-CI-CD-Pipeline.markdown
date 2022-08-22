---
layout: post
title:  "[CI/CD] CI/CD"
date:   2020-12-24 09:00:00
tags: development continuous_integration continuous_deployment continuous_delivery ci cd
categories: etc
---

# Modern Development Process's Problem
전통적인 개발 프로세스를 따르면서 우리 개발자들은 많은 어려움을 겪습니다.
일례로, 여러 피쳐들을 배포 주기에 맞춰서 merge하고, integrate하는 작업은 적게는 수 시간 
많게는 수 일이 걸릴만큼 추가적인 오버헤드가 걸립니다.

현대 개발 프로세스에서는 ***여러명의 개발자가 동일한 서비스에 대하여 각기 다른 피쳐들을 병렬적으로 개발하는 것을 기본적인 업무환경***으로 삼고 있기 때문에, 그러한 피쳐들을 머징하는 과정은 필수적입니다. 
다만, 이 머징을 수행할 때, "Merge day"를 따로 정하는 등 ***지루한 그러나 위험한 프로세스가 반복적으로 존재***하고 있기 때문에 개발자들에게 부담이 됩니다.

앞으로 우리가 살펴볼 CI/CD는 이렇게 Integration에 존재하는 문제점을 해결하고, 자동화함으로서 
개발자 개개인은 본인의 업무에 충실하도록 하고, 그 외의 작업들은 자동화된 시스템이 처리를 해주는 것을 의미합니다.

그렇다면, CI, CD란 정확히 무엇일까요?

# Continuous Integration & Delivery & Deployment
CI/CD라는 개념은 그리 어려운 것이 아닙니다.
다양한 툴 사용법과, 그것을 커스터마이징 하는 것이 어려울 뿐이죠.

저는 다음 그림이 CI/CD에 대한 모든 것을 담고 있다고 생각합니다.

![ci_cd_process.png](/static/assets/img/posts/cicd/ci_cd_process.png)

## CI
CI 라는 것은 **Continuous Integration** 을 의미합니다.
한국어로는 지속적 통합이라고 말하죠. CI를 잘 구축할 경우에는 각 개발자들이 변경한 내용들이 정기적으로 `Build`되고 `Test`되어 Origin Repository에 Integration 되므로, 위에서 얘기한 문제점들을 해소 할 수 있습니다. 즉, **Conflict**로 인한 다양한 문제점들을 미리 방지 할 수 있죠.

Integration이 되는 주기는 수 시간, 혹은 최소 하루에 한 번으로 정의되곤 합니다. 각각의 Integration은 위에서 언급하였듯, automated build/test 로 검증이 되고, 발생되는 문제나 에러를 빠르게 보고받고 해결 할 수 있지요.

즉, CI의 목표는 Integration Hell이라고 불리우는 Seamless 하지 않고, Smooth하지 않은 Integration Process를 Seamless, Smooth, 그리고 Automatic 하게 바꾸는 작업이라 불리워도 무방합니다.


## CD
CD 는 **Continuous Delivery** 혹은 **Continuous Deployment** 를 의미합니다. 
한국어로는 각기 지속적 제공, 지속적 배포라고 합니다.

먼저 Continuous Delivery 라고 일컬어질 때의 상황을 살펴볼게요.
Continuous Delivery 는 위의 CI를 통해 검증된 소스코드가 공유 Repository에 릴리즈되는 것을 의미합니다. 
이 과정을 통해서, 개발팀과 비즈니스 팀간의 커뮤니케이션 비용을 줄여줄 수 있습니다.

Continuous Delevery 는 개발자의 변경사항을 프로덕션 환경까지 자동으로 배포하는 것을 의미합니다. 
이를 통해 운영팀이 빠르고 쉽게 안전한 프로덕트를 프로덕션 환경에 배포할 수 있게 됩니다.

## CI/CD
흔히 CI/CD 파이프라인이라고 얘기를 할 때, 
Continuous Integration, Continuous Delivery 만을 의미할 때도 있고, Continuous Integration, Continuous Delivery 및 Continuous Deployment 까지 의미할 때도 있습니다.

결과적으로 이러한 파이프라인은 "개발자 개인"이 작업한 수정 사항들이 자동화된 빌드, 테스트를 거쳐 검증된 코드가 원격 저장소에 머지되고, 이를 비즈니스팀 혹은 엔드 유저까지 전달하는 일련의 과정을 포함합니다.

---

CI/CD 파이프라인을 잘 구현하게 되면, 애플리케이션 배포의 위험성을 크게 줄여주므로, 애플리케이션의 변경사항을 모두 한번에 릴리즈하지 않고, 작은 sub-tasks로 **잘게 쪼개어서 자주 배포** 할 수 있도록 해줍니다. 
이렇게 되면, 사용자의 피드백 등을 더욱 빠르게 적용 할 수 있을 뿐만 아니라, 프로덕션의 안정성도 크게 높여 줄 수 있습니다.

다만, 이러한 파이프라인 구축을 위해서는 자동화된 테스트, 세분화된 릴리즈 단계 등을 구축하는 것을 전제로 하기에 많은 노력이 필요합니다.


# CI/CD in 99.co
현재 제가 일하고 있는 [99.co](https://99.co) 에서는 **Travis CI** 를 통해 자동화된 빌드 및 테스트를 수행하고 있습니다.
**Microsoft AppCenter** 를 통해 각각의 Android, iOS 빌드들을 관리하고 있으며, 이를 통해 QA 팀 및 프로덕트 팀 멤버들과 커뮤니케이션하고 있습니다.
~특히 iOS 파트의 경우에는 ***Fastlane*** 을 통하여 AppStore에 릴리즈하는 프로세스를 구축하여 CI/CD 파이프라인을  완성한 단계입니다.~ (현재는 XCode Server를 통한 CI/CD 로 완전히 리뉴잉 하였습니다)

Android 파트의 경우에는 현재 자동화된 테스트를 조금 더 보강하는데 힘쓰고 있으며, *다양한 마켓*(Google PlayStore, Huawei AppGallery) 들이 존재하는 만큼, CD 의 경우에는 어느 정도 수동으로 개발자가 배포하고 있습니다.

2021년의 99.co 모바일팀의 주요 목표는 CI 프로세스 상에, 자동화된 테스트의 커버리지를 높여서 더욱 신뢰할 수 있는 프로덕트들을 Delivery하고, CD 프로세스 또한 보강하여 개발자가 아닌 "누구나"가 손 쉽게 프로덕션 레벨까지 배포하도록 현 파이프라인을 개선하는 것입니다.

위의 목표를 수행하며 얻은 다양한 경험을 다른 포스트로 올릴 수 있기를 바라봅니다.

모두들 2020년 수고많으셨고, Merry X-mas & Happy New Year!