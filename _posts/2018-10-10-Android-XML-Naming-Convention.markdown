---
layout: post
title:  "Android XML Naming Convention"
date:   2018-11-12 09:00:00
tags: android xml naming convention
categories: devstory
---
이 글에서는 Android XML Naming Convention에 대해서 다룹니다.

> Jeroen Mols의 블로그를 참고하여 적었습니다. 
> https://jeroenmols.com/blog/2016/03/07/resourcenaming/)


Android에서는 layout, string 등의 resource 등을 관리하기 위해 xml 파일이 많이 사용됩니다. 바로 아래와 같이 많은 xml 파일들이 포함된 것을 볼 수 있습니다.

![1.png](/static/assets/img/posts/xmlnaming/1.png)

android 개발을 진행하며 다양한 xml 들을 생성하게 되는데, 컨벤션이 없다면 수많은 xml들의 관리에 어려움을 겪을 수 있습니다. xml 네이밍에도 컨벤션이 필요한 이유죠.

각각의 xml 파일들은 아래와 같은 `원칙`을 통해 네이밍을 할 수 있습니다.

## Basic Principle
<b style="color:#367CA1">\<WHAT\></b>\_<b style="color:#79AE3D">\<WHERE\></b>\_<b style="color:#EC9D2F">\<DESCRIPTION\></b>\_<b style="color:#B469E3">\<SIZE\></b>

### \<WHAT\>
해당 파일이 나타내고자하는 컴포넌트(혹은 대상)를 지칭합니다. 예를 들어, fragment, activity, view, layout 등으로 명확하게 지칭 할 수 있습니다.

```
MainActivity -> activity
MenuView -> view
...
```


### \<WHERE\>
어플리케이션에서 논리적으로 어떤 부분을 표현하고자하는지를 나타냅니다. 예를 들어, Main화면, Login화면 등이 있겠습니다.

```
MainActivity -> main
LoginFragment -> login
MenuView -> view
...
```


### \<DESCRIPTION\>
어떤 엘리먼츠를 가리키는지를 나타냅니다. 예를 들어, title, name 등이 있습니다.


### \<SIZE\>
해당 리소스가 어떤 사이즈를 가리키는지를 나타냅니다. 예를 들어, 24dp, small 등이 있습니다.



## Cheet Sheet
![2.png](/static/assets/img/posts/xmlnaming/2.png)
<출처: [@MOLSJEROEN 블로그](https://jeroenmols.com/img/blog/resourcenaming/resourcenaming_cheatsheet.pdf)>