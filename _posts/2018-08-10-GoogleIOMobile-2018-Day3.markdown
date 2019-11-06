---
layout: post
title:  "[Android] Google for Mobile I/O Recap 2018 Day3"
date:   2018-08-10 07:00:00
tags: java Android AndroidP Android9 GoogleI/O 2018
categories: devstory
---
# AI & Cloud Session

6/25일부터 26일, 양일간 열린 Google for Mobile I/O Recep 2018 Seoul(이하 Google I/O) 참가후기에 대한 글입니다.

이번 글에서는 [지난 시간의 글 - 개발자 세션](/devstory/2018/08/10/GoogleIOMobile-2018-Day2/)에 이어서, AI 및 Cloud 세션에 대한 소개가 다뤄집니다.


### Posts
- [Google for Mobile I/O Recap_001](/devstory/2018/08/10/GoogleIOMobile-2018-Day1/) <br/>
- [Google for Mobile I/O Recap_002](/devstory/2018/08/10/GoogleIOMobile-2018-Day2/) <br/>
- [Google for Mobile I/O Recap_003](/devstory/2018/08/10/GoogleIOMobile-2018-Day3/) <br/>
- [Google for Mobile I/O Recap_004](/devstory/2018/08/10/GoogleIOMobile-2018-Day4/) <br/>



# 1. Dialogflow와 ML API를 활용한 챗봇 개발
SNS를 넘어선 메신저 사용자 !

messenger기반의 챗봇은 양방향 기반이기 때문에 날로 그 중요성은 높아질 것이다.


## 3가지 주요 사용 사례
- Customer Suppport (CS)
- Transactions
- Getting things done


## How to
- `Intent` 파악
- `Entity` 파악
- `Context` 참조

```
ex) I like to eat banana

intent : eat sth.
entity : banana

ex) How many calories are there?
A: There are 89 calories in a banana.

context: communication
```


Chatbot + ML API + Google Assistant

앞단의 UI는 Google Assistant 를 사용함
영단어는 Google Sheets에 넣었음
Translation API 문장 번역 기능


## Dialogflow
챗봇계의 표준처럼 쓰이고 있음
Google Assistant와 연동하면 너무 좋다.
백엔드 역할을 Dialogflow가 함.



## 예제 챗봇
- https://github.com/javalove93/dialogflow-mlapi


## Deep neural network
- DNN 이 나오기 전에는 모든 규칙을 다 입력을 해야함
    - 그래서 규칙 기반의 Chatbot 은 한계가 있다.
- 그만큼 학습이 매우 중요하다.


## Google ML API
- TensorFlow
- Cloud ML
    - Custom Models
    - TensorFlow를 여러개 돌릴 수 있는 Paas 형태의 cloud 플랫폼
- Pre-built Models



## 자연어 처리 Natural Language API
감정 분석(Sentimental), Syntax, Entities, Categories 등 분석이 가능


## 음성 인식 API Speech API
음성을 인식 Text로 변환.
구문힌트나 인식모드 등을 통해 정확도 향상
(전화통화 등 다자간 대화, 동영상 내부의 음성을 데이터셋으로 학습을 시킴)


## 사진 분석, 텍스트 인식 Vision API
사진을 분석해서 Entity, Text, 사람 및 표정, 장소, 부적절한 이미지 등을 분석함.



## Video Intelligence API
영상을 분석해서 entity를 추출하고 레이블과 해당 내용이 나온 장면 등을 분석

- https://cloud.google.com/translate
- https://cloud.google.com/natural-language
- https://cloud.google.com/speech
- https://cloud.google.com/vision








# 2. 모바일 개발자를 위한 TensorFlow Lite 소개

TensorFlow Lite : TensorFlow의 Mobile 버전

머신러닝이 왜 모바일 기기에 중요할까?

Search Maps Gmail Chrome Android Assistance .. 이미 많은 곳에 ML이 쓰이고 있음

It is optimized for device


- tensorflow : https://www.tensorflow.org/
- tensorflow lite : https://www.tensorflow.org/mobile/tflite/



## Google Inception Model 잘 설명해놓은 블로그
- https://norman3.github.io/papers/docs/google_inception.html

## Hardware Acceleration
Andorid : OpenGL
iOS : Metal


## How do i use tensorflow lite
Get a Model : download or train
Convert : the model to tensorflow lite
Write ops : if needed
Write app : (use client API)



## Limitations on model design
Currently limited to inference ops
~50 commonly used operations uspported
Supports MobileNet, InceptionV3, ResNet50, SqueezeNet, DenseNet, InceptionV4, SmartReply and others
Quantized versions of MobileNet, InceptionV3
Extensible design allows using 'custom defined' ops




## Conversion tips
Be sure to use a frozen graphdef (or SavedModel)
Avoid unsupported operators
Use visualizers to understand odel (TensorBoard & TensorFlow Lite Visualizer)




# 3. 모바일 개발자를 위한 ML Kit: Machine Learning SDK 소개
ML Kit : Machine Learning SDK 소개

올해 TPU 3.0 공개함. 현재 외부에서도 사용할 수 있음(직접 접근은 안되고, 제공하는 API를 이용하여 접근가능)


## ONNX (Open Neural Network Exchange)
- Caffe2 + PyTorch
- 공통된 규격

## MLMODEL (Code ML model, Machine Learning Model)
CoreML2 제공함


## ML Kit
Google's Machine Learning SDK

Tensorflow 를 쓰려면, Tensorflow의 스펙을 모두 파악해야하는 어려움이 있다.
그리고, 트레이닝을 해서 모델을 만들면 좋은 모델이면 400Mb~1Gb 까지 하는 데이터크기를 모바일에서는 감당할 수 없다. 결국은 추론 밖에 없는데 어떻게 연동을 해야할지가 막막할 수 있다.

그것을 바로 `MLKit`을 이용하여 해결한다.


Firebase 로 프로젝트 생성해서 바로 써볼 수 있다.
랜드마크 검색같은 경우에는 cloud 기반이고 나머지는 (얼굴인식, 텍스트인식, 바코드인식 등) serverless로 바로 실행 해 볼 수 있다.

SmartReply API, 고성능 얼굴 칸토어 인식은 출시 예정인 API이다.