---
layout: post
title:  "[JAVA] InvalidKeyException"
date:   2018-06-01 09:00:00
tags: java android encrypt AES InvalidKeyException
categories: devstory
---
Android에서 특정 값들을 암호화/복호화해야할 일이 발생하였다.
이에 javax 패키지의 `javax.crypto.*`, `java.security.*` 패키지의 모듈들을 이용하여
암호화/복호화 모듈을 만들다가 발생한 에러에 대해 설명하고자 한다.


# 