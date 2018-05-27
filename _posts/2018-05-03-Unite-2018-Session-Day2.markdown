---
layout: post
title:  "[Unite] 2018. Day2"
date:   2018-05-03 09:00:00
tags: seminar unity unite unite2018
categories: devstory
---

안녕하세요. 지난 포스트에 이어서, 이번에는 Unite 2018 Day2 후기를 올리도록 하겠습니다.
Day2이자, 마지막 아젠다가 열렸던 날이 되겠네요.

[[Unite] 2018. Day1](/devstory/2018/05/02/Unite-2018-Session-Day1/)
[[Unite] 2018. Day2](/devstory/2018/05/03/Unite-2018-Session-Day2/)


# Unite 2018 Sessions Day2
- Date : 5/4, 금요일
- Session Info : https://www.uniteseoul.com/2018/agenda.aspx#agenda2


---
# 1. Low level understanding of iOS memory
- Time : 5/4, 11:00 ~ 11:50
- Presenter : Valentin Simonov / Unity / Field Engineer


## Physical Memory (RAM)

## Virtual Memory (VM)
iOS apps don't work iwth physical Memory directly, they work with Virtual Memory.

## Resident Memory
- An app can allocate a block of memory in VM but not use it ("reserve")
- The app has to modify the allocated VM for the OS to map it to PM("commit")
- Resident Memory is the total amount of PM used by the app

## Clean Memory and Dirty Memory
- Clean Memory - read only pages of Resident Memory which iOS can remove and reload
- Dirty Memory - everything else in RM
- Apps can share Clean Memory pages(like, system frameworks)

## Swapped (compressed) Memory
iOS doesn't have a page file and can not dump rarely used VM pages to disk.
But i can compress them and store in some other region of PM.
SCM is a compressed part of the app's Dirty Memory.


## Native (Unity) Memory
- Unity is a C++ engine with a .NET Virtual Machine.
- Native Memory- part of Malloc Heap(VM Region) used for all Unity's allocations.

## Native Plugins
- Native plugins do therir own allocations in Malloc Heap

## Mono Heap
- A part of Native Memory allocated for the needs of the .NET Virtual Machine.
- 


## iOS Memory Management
- iOS is a multitasking OS
- When the amount of free PM gets low, iOS Starts trying to reduce memory pressure
    - 1. Removes Clean Memory pages(they can be safely reloaded later)
    - 2. I fthe app takes too much Dirty Memory, iOS sends memory warnings.
    - 3. If the app fails to free resources, iOS terminates it.
- We don't know the details about this algorithm.



## Minimize the Size of Dirty Memory !
- Reduce the amount of objects contrubuting to Dirty Memory.
- Note : some data can be compressed better.
- Reasonable limits of Dirty Memory:
    - 180Mb for 512Mb devices,
    - 360Mb for 1Gb devices,
    - 1.2Gb for 2Gb devices.
- ... but, iOS can still kill the app.


## Tools
### Unity
- Unity Profiler
- MemoryProfiler
    - https://bitbucket.org/Unity-Technologies/momoryprofiler
### Xcode
- Xcode Debug View



---
# 2. Jump start your Game in JAPAC market with CLOUD
- Presenter : 
    - 조성범 / Alibaba Cloud / 한국대표
    - 고성민 / SB Cloud / 인큐베이션 프로그램 리더 - Softbank cloud


## Alibaba - Softbank

### 숫자로 보는 알리바바와 소프트뱅크 관계
```
2000년도
10분간의 손정의-마윈 미팅
20명 직원
200억 투자받음
```


## Alibaba Cloud
### 시장
- 10만 회원수, 18개 글로벌 리전(아시아에 특히 집중됨)
- 글로벌 3위의 클라우드 업체
- 중국 점유율 47.6%
- 알리바바는 인도네시아에 거의 독점적으로 서비스를 하고 있음(애저나 aws는 들어와있지 못함)

## 서비스
- AWS와 거의 유사한 서비스를 제공중임
- 11월 11일, 24시간 행사 : 광군제 (double 11)
    - 서버 대수가 100만대를 지원함.
    - 초당 32만 5천건의 컴퓨팅 파워
    - 접속 유저 5억
    - 가입 유저 9억
- 알리바바 그룹내 주요 서비스가 알리바바 클라우드 위에서 운영중
    - 알리익스프레스, 티몰, 유큐, 등등..
- Security
    - 1억개의 웹서비스가 올라가있음
    - 800억개의 공격을 매일 방어중


## Softbank Cloud
- Softbank - Alibaba가 공동 출자한 회사, SB Cloud
- 일본 시장내에서는 소프트 뱅크가 알리바바를 대행하고 있다고 함
- 일본에 진출하고자 한다면 ***소프트뱅크***를 통하면 된다.
- 소프트뱅크 자회사가 760개가 넘는다고 한다. 즉, 일본 전체를 아우르고 있는 거대 기업.
- 


## 느낀점
- Alibaba Cloud(정확히는 Alibaba, Jack Ma)는 SB(Softbank와 엄청 가까운 사이였구나.)
- `9Apps` - Alibaba에서 운영중인 AppStore






---
# 3. eBay가 바라보는 커머스의 미래 - 온라인을 넘어 AR로
- Presenter : 이진용 / eBay Korea / 부문장

- Commerce 진화
    - 물류 !

## Next Commerce
- Paradigm Shift: What's Next?
    - 넓은 공간. 많은 상품. 빠른 거래.
        - Markey -> mall -> Home Shopping -> gmarkey/11st/auction/coupang/interpark -> mobile -> ??
    - 경험 = 1/정보 : Change of Quality
        - 상품의 정보가 엄청나게 많이 노출되고 있는데, 충분한 정보 이상의 것은 오히려 구매자의 불편을 야기함.
        - Next Commerce에서는 소비시 "경험"의 부재를 해결해야한다.
        - ex) 
            - 상품을 샀는데, 생각보다 엄청 크기가 커서, 작아서, 조금은 달라서 당황한 경험들이 있다.
            - 가구를 샀는데, "사진"에서 보는 것과는 다르게 너무나도 안어울린다. 왜냐면 사진은 "컨셉"을 최대한 맞춰서 찍은 사진이기 때문.

## From Mission to Inspiration Shipping
- Hard Goods(노트북, 컴퓨터 등등) => Soft Goods(옷, 신발, 악세서리) => Expreience(에펠탑을 찍는 카메라, 크루즈 여행시 입기 좋은 가디건 등등)

## New Experience
- How ?!
- VR/AR - New Reality
    

### AR in Commerce
- Better User Experience
- Better Customer Engagement
- Reduce Return Rate
- ZBetter Zcustomizable


## Future of Commerce
- 기기에 종속적인 "쇼핑"이 아니라, 자연스러운 쇼핑
    - 알렉사 같은 느낌. (대화형 기기)
    - 사람 행동 등 인식하는 IoT기기
- Targeted and Personalized
    - 소품종 대량생산 -> 다품종 소량생산
    - Targeted Offer


## AR in Ebay

### 중고
- 미국/글로벌에는 개인간 거래가 매우 활발(한국은 중고나라 같은 느낌인데, 외국은 더 활발하다고 함)
    - 문제점
        - 어떻게 포장을 할까?가 고민이 됨. 현실적인 고민임.
    - 해결방안
        - Shipping Box + AR
        - 물건을 카메라에 비추면, 가장 fittable 한 표준 박스가 뜨고, 가격도 뜬다.
        - 해당 기능을 통해 박싱 및 택배가능

## 느낀점
- 커머스 시장에도 VR/AR이 조금씩 스며들고 있다 !
- 언제쯤 상용화되어서 우리 옆으로 다가올 수 있을까?



---
# 4. 게임으로 더 밝은 세상을 꿈꾸는 게임 "애프터 데이즈" 제작 후기
- Presenter : 도민석 / GamBridzy / 대표

## AfterDays
- 지진을 견뎌낸 네팔 커피농부들의 이야기. (2015.04.25)
- http://afterdaysgame.com/

## 네팔 지진
- 2015년 04월 25일에 일어난 네팔 지진
- 네팔 건물의 70%가 무너졌다고 함.



## 고민한 점들
- 처음에 창업을 할 때의 마인드
    - `게임을 통해 사람들의 이타심을 강화 할 수 있을까?`
        - 지금와서 돌아보니, 너무 오만한 질문
    - 다음과 같이 바뀜
    - `게임이 주는 즐거움이 세상을 밝히는 에너지가 될 수 있지 않을까? & 우리가 더 잘한다면!`

- 여러 사람들의 부정적인 피드백
    - 왜 네팔인데
    - 왜 한국 사람은 안돕는건데?
    - 왜 게임으로 돕겠다는 거야?
    - 게임은 재미만 있으면 되는거 아니야?
    - 차라리 개발 비용을 네팔에 기부하는게 더 낫지않아?
- 실제 "지진"의 상황을 재현하면, "게임성"이 떨어진다. 이 딜레마를 어떻게 할 것인가?
    - 헬기로 구호품을 전달할 때, 악천후 속에서 전달하는 것 자체가 엄청난 헬이다. 이걸 그대로 유저에게 전달하려면, 난이도 헬의 미션이 됨.
- 하지만. 게이머들의 응원이 있었기에 버텼다.

## 지속 가능한 소셜 임팩트 게임 생태계 구축
- 어떻게 지속 가능할 수 있을까?
- Game <- Contents, Marketing -> Partners(NGO, NPO, Company)
- Game <- Funding, Play -> Player
- Game <- Feedbck, Story -> Local
- Partners <- Growth, Support -> Local
- Player <- Trust -> Partners


## 제작비 확보
- 약 3400만원 (와디즈 140만원, 텀블벅 320만원, 한국 사회적기업 3,000만원)
- 160명의 후원자




## 느낀점
- Unite 2018의 마지막 세션
- 보면서 눈물이 났다.
    - 선한 의도
    - 부족한 사업력, 혼란스러운 개발 기간들
    - 취지에는 공감하나, 평가에는 철저한 유저들의 리뷰.
        - 현실적으로 도저히 어떻게 할 수 없는 한계점과 이 한계를 지적하는 유저들의 목소리를 들었을 때, 얼마나 가슴이 아팠을까.
    - 그 힘듦으로 번 돈, 전액 300만원을 네팔에 기부했다고 할 때의 감격.
        - 금액은 얼마 되지 않을 수도 있다... 하지만, 그 "전부"를 기부한다는 것이 얼마나 큰 마음인지 느껴졌다.
- 한 유저의 혹독한 리뷰후 반년이나 지나서야 업데이트를 할 수 있었던, "현실"


