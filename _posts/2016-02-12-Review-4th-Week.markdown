---
layout: post
title:  "리팩토링과 읽기 좋은 코드"
date:   2016-02-12 22:00:00
categories: [Life]
tags: [4thWeek,Hello_NHN_Ent,기술교육]
---
### 설 연휴 등으로 바빴던.. 한주

#### 리얼서버 리펙토링 JUnit
  예전 포도트리에서 일하면서, 여러가지를 배웠던 것중에 하나가, 바로 개발용/서비스용 서버가 따로 나뉜다는 것이었다. 물론, 가비아에서도 php4, php5, mysql for dev, mysql for service 등 수많은 서버들이 dev, real 로 나뉘어져있음을 알았다. 
  NHN Ent. 또한 이와 별반 다르지 않았다. 아니, 오히려, 다른 곳보다 더욱 진일보되어서 local/dev/alpha/beta/real 용 서버를 엄격하게(?!) 구분해놓고 사용하는 것에서 나름 신선함과 규모를 느끼었달까. 이를 경제학적으로 보면, 규모가 크니까... 할 수 있는 규모의 경제학이랄까? 
  작은 스타트업을 운영했을 당시 기억으로는 개발/서비스용 서버는 커녕, 로컬/서비스 만 해도 벅찼다.(비용도, 노력도...) 나름 큰 회사이다보니, 이러한 step과 sequence를 만들어놓고 사용할 수 있도록 해놓은 것 같다.
---

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- mrkarl ad 001 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-5338438534915104"
     data-ad-slot="1277261876"
     data-ad-format="auto"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>


#### "읽기좋은 코드" 를 듣고서...
> 6개월 후의 나는, 지금 코드를 짜고있는 내가 아니다.

라는 말처럼, 무서운(?!)것이 어디있을까.
지금의 내 코드를 보면, 솔직하게, 하나의 프로젝트/파일을 까놓더라도 한명이서 짰다는 느낌이 들지를 않는다. 그것은 아마 코딩컨벤션이라는 개념이, 내 손가락들의 자아에 의해서 정해지기 때문이 아닐까. 두뇌에서 명령하는 일관적인 규칙에 따라서, 개발을 해야겠다는 다짐을 해본다.

끄읏