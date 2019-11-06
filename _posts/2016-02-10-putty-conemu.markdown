---
layout: post
title:  "[Putty] ConEmu"
date:   2016-02-10 21:00:00
tags: etc oauth Authentication Authorization
categories: devstory
---
#### ConEmu
Putty 한개 켜놓고, 서버 접속해서 이리저리 다니면서 명령 날리기 귀찮으시죠?
Putty에도 탭기능이 있어서, 이리저리 다니면 얼마나 좋을까요.
이걸 지원하는 Putty Container 라는 프로그램이 있는데,

저는 Conemu라는 프로그램을 소개하고자해요.

Putty Container 는 여기서 받으실 수 있습니다.
> http://blog.naver.com/kurishin/60094512557

Conemu는 일단, BSD 라이선스라서, 상용 소프트웨어에서도 사용할 수 있다고 생각해서, 공유하고자 합니다 !!

프로그램은 여기서 받으실 수 있구요 !
>http://www.fosshub.com/ConEmu.html

Putty와 연동하는 부분은 여기에서 보실 수 있습니다.
>http://www.songtory.com/post/001005/1/220

푸티를 연동하는 부분에서 파일 이름만을 이렇게
```shell
$ putty.exe -new_console
```

적고서 연동하는 방법도 있지만, 그냥 Full Path를 적어주는것도 편하리라 생각합니다. 다음처럼요.

```shell
$ D:\Putty\putty.exe -new_console
```

Save Settings를 누르고 Split을 적용하면 !
![split screen](https://codingreflection.files.wordpress.com/2014/04/conemu-split-screen.png)

이러한 화면을 보실 수 있습니다.


---

서버에서 작업이 많아지며, 콘솔을 오래 보게 될텐데, 이렇게 다중창 작업으로 효율을 높이는게 중요해보여요 !