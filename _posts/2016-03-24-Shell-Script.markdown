---
layout: post
title:  "[Linux] About Shell"
date:   2016-03-24 03:00:00
tags: linux shell bash sh script
categories: devstory
---
나는 shell을 좋아한다. 뭔가 있어보이는 듯한 허세가 너무 좋다. 그렇다고 쉘이나 vi를 자주 애용은 하지 않지만... 무튼 쉘에서 작업을 할 땐 뭔가 행복하다.

이번 시간에는 이런 "허세감", "관종"감(?)을 이끌어내는 shell을 다뤄보자 !


# 쉘 Shell

## What the Shell?
- Shell : In computing, a **shell** is a user interface for access to an OS's services.

즉, OS의 서비스에 접근하기 위한 유저 인터페이스이다 ! (명령어 해석기, 커맨드 인터프리터)
그렇다면, Unix/Linux/Windows 등 `모든 OS에서 필수적`이겠네 !!?

**`Shell`** 이 부릅니다. ♪♪ <연결고리> 너와 나의 연결 고리 ! `커널과 사용자의 연결 고리` !!

## Shell의 종류
- Mac/Unix/Linux : bash, sh, ksh, csh, tcsh, zsh ...
- Windows : explorer.exe, cmd.exe
- 어릴적했던, 천리안/나우누리/새롬데이타맨프로 등도 쉘인 것이다 !!(이쯤이면 친숙한 이미지 !!)

![새롬데이타맨프로](https://raw.githubusercontent.com/karl-park/karl-park.github.io/7cf7cf1939370529e6c885a934ac0776ebbff70b/assets/images/shell/seromdatamanpro.jpg)

#### [참고]
> bash : Bourne Again SHell, BASH
> sh : Bourne SHell, SH

##  Shell 확인/수정
### 확인

아래와 같이 명령을 날려서 알 수 있다.

```bash
$ echo $SHELL
/bin/bash
```

### 수정

쉘 변경 : /bin/bash -> /bin/csh
기본사용쉘은 `/etc/passwd 의 마지막 필드`에 지정되어 있다.

```bash
$ chsh
Changing shell for karlpark.
Password:
New shell [/bin/bash]: /bin/csh
Shell changed.

$ cat /etc/passwd | grep karlpark
karlpark:x:500:500:SungSoo:/home/karlpark:/bin/csh
$
```


## Shell 의 환경변수

![env](https://raw.githubusercontent.com/karl-park/karl-park.github.io/7cf7cf1939370529e6c885a934ac0776ebbff70b/assets/images/shell/env.PNG)

**PATH** : Shell의 실행파일들이 있는 디렉토리 리스트(콜론으로 구분)
**CLASSPATH** : JRE(Java Runtime Environment)를 사용하는 Jar, Java Class파일들이 저장되어있는 환경변수
**JAVA_HOME** : Java가 설치되어있는 기본 디렉토리
**PWD** : 파이릿스템의 현재디렉토리
**USER** : 로그인한 사람 이름
...

```bash
$ export MY_ENV=/home/karlpark/test/myenv
```

### PATH 환경변수
PATH : 환경변수를 담는 환경변수랄까.

1. 사용자 명령 입력 
2. Shell 내부명령어인지 Check 
3. 외부명령어일 경우 PATH 환경변수를 참조하여, 실행

```bash
$ echo $PATH
/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin
```

### PATH 설정

쉘의 환경변수는 로그인 할 때, 설정된다. 사용자 환경은 profile에서 설정되는데, profile은 크게 2가지가 있다.

1. 글로벌 프로파일
2. 사용자 계정 프로파일

Linux bash 기준으로
\- 글로벌 프로파일은 /etc/profile, /etc/bashrc
\- 사용자 프로파일은 ~/.bashrc ((사용자홈디렉토리)/.bashrc )
(sh 는 /etc/profile과 ~/.profile)

### Aliases

- Aliase 등록 : `alias name=command`
- Aliase 삭제 : `unalias name`

```bash
alias l="ls -l"
alias ll="ls -al"
alias ~="cd ~"
alias md=mkdir

unalias md
```

## Shell Script
Script : `interpret 방식`으로 동작하는 컴파일되지 않은 프로그램

- Shell Script, Perl Script, JavaScript, ...
- 위의 Shell, Perl, Javascript 들이 의미하는 것은 바로, 스크립트를 읽어 실행해주는 엔진 !
    -  Shell Script -> OS의 bash, sh 등이 엔진
    -  Perl Script -> Perl이 인터프리트 엔진
    -  JavaScript -> 웹브라우저가 인트프리트 엔진

### 컴파일 언어와의 차이

```c
// C Language
// hello.c
#include <stdio.h>
int main(){
    printf("helloworld");
    return 0;
}

// Shell Script
// hello.sh
#!/bin/bash
echo "helloworld"
```

위의 두 프로그램 모두 "helloworld"라는 문자열을 출력해주지만,
근본적으로 두 프로그램은 실행 과정있어서 큰 차이를 가진다.

|C|Shell|
|-|-|
| - hello.c가 컴파일되고 링크되어서 hello 라는 파일을 만든다.<br/> - 컴파일되고나면, 바이너리 구조로 변형되어서, vi/cat 과 같은 명령으로 내용을 확인 할 수 없다.<br/> - "기계어"로 변환되었기 때문에, **"Kernal"**에 의해서 실행된다.<br/> - 해당 프로그램은 정식 프로세스로 생성된다.| - 컴파일되지 않으므로, 따로 변환이없고, 실행퍼미션만 주면 실행이 가능하다.<br/> - 정식 프로세스로 생성되지는 않는다.|

### 사용자 입력받기

```bash
echo -n "input somthing: "
read input
echo "your input : $input"
```

### 조건문

```bash
if [ $num -gt $compare ]
then
    echo "$num은 $compare 보다 큽니다."
    exit
elif [ $num -lt $compare ]
then
    echo "$num은 $compare 보다 작습니다."
    exit
else
    if [ $num -eq $compare]
    then
        echo "$num은 $compare 과 같습니다."
        exit
    else
        echo "$num은 $compare 과 같습니다."
        exit
    fi
fi




echo -n "Y or N :"
read input
case $input in
    y|Y)
        echo "YES !"
        echo "YES YES !";;
    n|N)
        echo "NO !"
        echo "NO NO !";;
    *)
        echo "Wrong !"
        break;;
```

###  반복문

```bash
for i in 1 2 3 4 5 6 
do
    echo $i
done

list = `ls ~/data/images`
for file in `echo $list`
do
    echo $file
done

echo -n "Please type the number: "
read num
while [ $num -gt 0 ]
do
    echo $num
    num = `expr $num - 1`
done
```

### 함수

```bash
two_sum_fun()
{
    echo "1st arg : $1"
    echo "2nd arg : $2"
    
    sum= `expr $1 + $2`
    
    return $sum
}

function helloworld()
{
    echo "helloworld"
}

two_sum_fun 1 2
result = $?

helloworld
echo "result = $result"
```
