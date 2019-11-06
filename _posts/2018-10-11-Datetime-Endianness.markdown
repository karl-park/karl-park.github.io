---
layout: post
title:  "국가별 DateTime Endianness "
date:   2018-10-11 09:00:00
tags: etc android ios locale time datetime endianness 엔디안
categories: devstory
---
이번 시간에는 국가별 `DateTime Endianness`(국가별 날짜 포멧) 에 대해서 알아보겠습니다.
많은 서비스들에서 "날짜"를 사용자에게 나타내어야하고, 지역화된 날짜 문자열을 보여주어야하는 이슈가 있습니다.

예를 들면, epochtime `1533261600000`에 대해서 한국과 미국, 영국의 DateTime String은 다음과 같습니다. (GMT+0)
- 한국 : **2018-08-03 02:00** (YYYY-MM-DD)
- 미국 : **08-03-2018 02:00** (MM-DD-YYYY)
- 영국 : **03-08-2018 02:00** (DD-MM-YYYY)

이렇듯 지역마다 `엔디안이 다른 이슈`가 있기에 사용자의 지역(혹은 타임라인)을 획득한 후, 정확하게 노출시켜줘야할 필요가 있습니다.

---

국가별 날짜 포멧을 살펴보기 전, 가볍게 "엔디안"의 개념부터 보고 가도록 할게요.
이미 엔디안에 대해 아시는 분은 스크롤 쭈욱 내려서 `국가별 날짜 포멧` 란으로 가시기 바랍니다.


## 엔디안이란 (Endianness)
컴퓨터 공학을 배우면, 초기에 배우는 개념이 "엔디안" 입니다. 엔디안은 메모리와 같은 1차원 공간에 연속된 대상을 배열하는 방법을 뜻하며, 흔히 *바이트 순서* 를 나타내는데 쓰입니다. 본문에서는 ***날짜를 배열하는 순서*** 를 나타내기 위해 엔디안이라는 표현법을 쓰고 있습니다.

엔디안은 크게 **빅 엔디안(B)**, **미들 엔디안(M)**, **리틀 엔디안(L)** 으로 나눌 수 있습니다.

예를 들어서, 메모리주소 0x1234를 표현하기 위해서는 다음과 같이 표현 할 수 있습니다.

| Endianness | 0x1234 | 0x12345678 |
| -------------- | ---------- | ----------------- |
| B | 12 34 | 12 34 56 78 |
| M | - | 34 12 78 56 <br/> or <br/> 56 78 12 34 |
| L | 34 12 | 78 56 34 12 |


본문에서는 "날짜"값을 나타내기 위해 엔디안을 언급하므로, 다음과 같이 표현 할 수 있겠습니다.

| Endianness | 1533261600000 (epoch time) |
| -------------- | --------------------------------------- |
| GMT+0 기준 | 2018-08-03 02:00 |
| B | **Y M D** : 2018-08-03 02:00 |
| M | **M D Y** : 08-03-2018 02:00 <br/> or <br/> **Y D M** : 2018-03-08 02:00 |
| L | **D M Y** : 03-08-2018 02:00 |


## 국가별 날짜 포멧
국가별 날짜포멧은 다음과 같습니다. 대략적으로 ***동아시아권은 "빅 엔디안"*** 을 사용중아고, ***그 외의 나라들은 "리틀 엔디안"*** 표기법을 사용하고 있음을 알 수 있습니다. ***미국과 같은 경우는 "미들 엔디안"*** 을 사용중이네요.

![1.png](/static/assets/img/posts/endianness/1.png)

<출처: [위키피디아](https://en.wikipedia.org/wiki/Date_format_by_country)>

--- 

각 국가별로 어떤 표기법을 지원하는지에 대한 자료는 아래의 링크를 참조하시길 바랍니다.

링크 : [위키피디아 ](http://calendars.wikia.com/wiki/Date_format_by_country#Listing)


## 어떻게 표현할까
각 국가별 날짜 포멧이 상이함을 알았다면, 그것을 어떻게 정상적으로 표현할지가 관건인데요, 각 플랫폼에서는 해당 디바이스의 "로케일/타임라인" 정보를 가지고 있고 이를 통해 최적화된 날짜포멧을 뿌려주는 API들이 존재합니다.


### Android

다양한 DateFormatter가 존재할 수 있지만, 그 중 java.text.DateFormat 메소드 중심으로 설명을 하겠습니다.

우선, Device의 Locale 정보를 받아와야합니다. Android API 18(Android 4.3, 젤리빈)에서 지원하는 `DateFormat.getBestDateTimePattern(Locale locale, String skeleton)` 메소드를 이용하면 정해진 스켈레톤(두번째 인자)을 따르는 locale 정보 DateFormat을 리턴해줍니다.
리턴되는 dateFormat 객체를 이용하여 DateTimeString을 얻을 수 있습니다.

getBestDateTimePattern()의 두번째 인자인 'skeleton'에 들어가는 심볼은 다음 링크에서 확인할 수 있습니다.
- [Unicode Date Field Symbol Table](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table)

```java
Date date = new Date(); // current date time
Locale locale = Locale.getDefault();

SimpleDateFormat dateFormat;
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR2) {
    String skeleton = "MMddyyyyhhmmssaa"; // "MMddyyyyhhmmssaaZZZZ"
    String pattern = DateFormat.getBestDateTimePattern(locale, skeleton);
    dateFormat = new SimpleDateFormat(getDateFormat(locale));
} else {
    // API19 미만일 경우에는
    dateFormat = new SimpleDateFormat("MMM/dd/yyyy hh:mm:ss aa");
}

String dateTimeStr =  dateFormat.format(date); // Locale에 따라서, 알맞는 날짜 형식을 리턴해줍니다.(API 18이상)
```



---------------

아래에서는 안드로이드에서 사용하는 핵심 Endianness API를 다룹니다.

## 사용하는 핵심 APIs
- **Date.toLocaleString()** (deprecated)
- **Date.toGMTString()** (deprecated)
- **DateFormat.getDateTimeInstance(int dateStyle, int timeStyle, Locale locale)**
- **DateFormat.getBestDateTimePattern(Locale locale, String skeleton)** (Android API 18 이상)


> 주의 !
> 
> 아래 예시에서처럼, **java.text.DateFormat** 과 **android.text.format.DateFormat** 을 구분해서 사용해주어야합니다.
> 


## 예시
```kotlin
// prerequisite
val epoch = 1533261600000
val date: Date = Date(epoch)
val locale = Locale.getDefault()
```

### Locale : ko_KR
```kotlin
val dateStr1 = date.toLocaleString() // 2018. 8. 3. 오전 11:00:00
val dateStr2 = date.toGMTString() // 3 Aug 2018 02:00:00 GMT

// java.text.DateFormat
val formatShort = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.SHORT, locale)
val dateStr3 = formatShort.format(date) // 2018. 8. 3. 오전 11:00

val formatMedium = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.MEDIUM, locale)
val dateStr4 = formatMedium.format(date) // 2018. 8. 3. 오전 11:00:00

val formatLong = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.LONG, locale)
val dateStr5 = formatLong.format(date) // 2018. 8. 3. 오전 11시 0분 0초 GMT+09:00


// android.text.format.DateFormat
// equal and greater than API 18 (JELLY BEAN MR2, API 18)
val dateStr6 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaa").format(date) // 2018. 08. 03. 오전 11:00:00

val dateStr7 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaaZZZZ").format(date) // 2018. 08. 03. 오전 11:00:00 GMT+09:00
```


### Locale : en_US
```kotlin
val dateStr1 = date.toLocaleString() // Aug 3, 2018 11:00:00 AM
val dateStr2 = date.toGMTString() // 3 Aug 2018 02:00:00 GMT

// java.text.DateFormat
val formatShort = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.SHORT, locale)
val dateStr3 = formatShort.format(date) // Aug 3, 2018 11:00 AM

val formatMedium = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.MEDIUM, locale)
val dateStr4 = formatMedium.format(date) // Aug 3, 2018 11:00:00 AM

val formatLong = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.LONG, locale)
val dateStr5 = formatLong.format(date) // Aug 3, 2018 11:00:00 AM GMT+09:00


// android.text.format.DateFormat
// equal and greater than API 18 (JELLY BEAN MR2, API 18)
val dateStr6 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaa").format(date) // 08/03/2018, 11:00:00 AM

val dateStr7 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaaZZZZ").format(date) // 08/03/2018, 11:00:00 AM GMT+09:00
```

### Locale : fr_FR
```kotlin
val dateStr1 = date.toLocaleString() // 3 août 2018 11:00:00
val dateStr2 = date.toGMTString() // 3 Aug 2018 02:00:00 GMT

// java.text.DateFormat
val formatShort = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.SHORT, locale)
val dateStr3 = formatShort.format(date) // 3 août 2018 11:00

val formatMedium = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.MEDIUM, locale)
val dateStr4 = formatMedium.format(date) // 3 août 2018 11:00:00

val formatLong = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.LONG, locale)
val dateStr5 = formatLong.format(date) // 3 août 2018 11:00:00 GMT+09:00


// android.text.format.DateFormat
// equal and greater than API 18 (JELLY BEAN MR2, API 18)
val dateStr6 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaa").format(date) // 03/08/2018 à 11:00:00 AM

val dateStr7 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaaZZZZ").format(date) // 03/08/2018 à 11:00:00 AM GMT+09:00
```

### Locale : ja_JP
```kotlin
val dateStr1 = date.toLocaleString() // 2018/08/03 11:00:00
val dateStr2 = date.toGMTString() // 3 Aug 2018 02:00:00 GMT

// java.text.DateFormat
val formatShort = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.SHORT, locale)
val dateStr3 = formatShort.format(date) // 2018/08/03 11:00

val formatMedium = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.MEDIUM, locale)
val dateStr4 = formatMedium.format(date) // 2018/08/03 11:00:00

val formatLong = DateFormat.getDateTimeInstance(DateFormat.DEFAULT, DateFormat.LONG, locale)
val dateStr5 = formatLong.format(date) // 2018/08/03 11:00:00 GMT+09:00


// android.text.format.DateFormat
// equal and greater than API 18 (JELLY BEAN MR2, API 18)
val dateStr6 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaa").format(date) // 2018/08/03 午前11:00:00

val dateStr7 = SimpleDateFormat(DateFormat.getBestDateTimePattern(locale, "MMddyyyyhhmmssaaZZZZ").format(date) // 2018/08/03 午前11:00:00 GMT+09:00
```