---
layout: post
title:  "ATS - Checking Security"
date:   2016-09-06 03:00:00
tag:
- ATS
- iOS
- Objective-C
ios: true
---

# Checking Security : ATS

## ATS Check
> /usr/bin/nscurl --ats-diagnostics --verbose https://apple.com
PASS

> /usr/bin/nscurl --ats-idagnostics --verbose https://webholic.kr
FAIL

This command will tell you which settings of info.plist will success.

you should catch these concepts about http://, https://, and https:// (with PFS).




ATS 설정을 함에 있어서, Arbitary Loads 를 YES로 켜놓으면 그 얼마나 쉽겠냐만 !!
보안상 쥐약.. 취약.. 취약 !! 하니껜 ! ATS 설정을 제대로 하자구 !!
더군다나 내년(2017)부터는 TLSv1.2 가 의무라규 !!

