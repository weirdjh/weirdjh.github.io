---
layout: page
title:  "Apache의 프로그램 배포"
date:   2018-01-30 20:00:00
category: knowledge
tags: checksum signature apache
---

Maven의 [download 페이지](http://maven.apache.org/download.cgi) 를 보면 source + binary / link + checksum + signature 로 이루어진 테이블이 있다. 인터넷을 오래한 짬으로 어떤 것을 선택해야 할지는 굳이 다른 것들이 무엇인지 몰라도 필요한 링크를 선택할 수 있었지만... 어떤 역할을 하는 링크들인지 궁금해서 알아보고자 한다.

<!-- more -->

---
### 1. source / binary release  


  * source : compile되지 않은 raw상태의 소스를 의미한다. Linux의 경우 환경에 따라 실행파일이 실행이 될 수도 안 될 수도 있기 때문에, source release를 따로 배포하는 듯 하다. 

  * binary : compile된 상태로 배포한 것이다.

  *(stackoverflow의 한 현자는 human readable - machine readable이라고 구분했다.)*

<br>

---

### 2. 동반되는 파일들


apache가 배포하는 모든 파일에는 checksum과 signature를 포함한다고 한다. 이제 대한 설명은 apache에서 [문서](https://www.apache.org/dev/release-signing)로 만들어놓았다. 궁극적인 목적은 파일이 깨지지 않았는지, 배포한 상태의 파일 그대로인지 검사하는 것이다.  
 

<br>

### 2.1. checksum 
 
`.md5`라는 생소한 확장자를 가지고 있다. 파일을 받아보면 다음과 같은 내용을 가지고 있다. 

> b8d8d49d8178734124c4ff6f3a409d3d  

md5는 Message-Digest algorithm 5의 약자로, 문자열을 읽어들여서 128bit로 출력을 해주는 암호화 해시 알고리즘이란다.
파일을 1024bit씩 읽어들이고, 해당 알고리즘을 이용해서 128bit의 string으로 계속해서 update하는 방식이다. (파이썬으로 된 [md5변환 스크립트](https://pythonadventures.wordpress.com/2011/11/17/md5-hash-of-a-text-file-crack-an-md5-hash/)이다.)
보안상 취약하다고 하며, 무결성 검사 목적으로 종종 사용된다고 한다. 

![md5]({{site.url}}/assets/knowledge/md5.png)

받은 파일이 깨지지 않고 정상적으로 받아졌음을 확인할 수 있다.

(참고로, md5는 완벽한 무결성검사가 아니고 예외가 있다고 한다. 해시의 특성상 서로 다른 값을 넣으면 서로 다른 값이 나와야 하는데, 그렇지 않은 경우가 생기는 것을 collision라고 한다고 한다. [md5의 collision을 확인](https://www.mscs.dal.ca/~selinger/md5collision/)해볼 수 있다.)

<br>

### 2.2. signature 

 `.asc`확장자를 가지고 있다. 역시 처음보는 확장자였는데, PGP(Pretty Good Privacy)에 의해 사용되는 확장자로서, armored ASCII file의 약자이란다. apache는 signature를 위해 PGP를 대체할 수 있는 OSS인 GnuPG를 사용하고 있다.

signature의 용도는, 파일이 배포 될 당시와 비교해서 변경되었는지 확인하고 파일의 owner가 보증하는 파일의 내용을 보증하는 것이다.

user는 apache에서 같이 배포한 [public key](https://www.apache.org/dist/maven/KEYS)를 통해서 배포된 asc의 암호화를 풀고,  release된 파일이 valid한지 검사할 수 있다.

*local에 public key들을 import한 후,*  
![key]({{site.url}}/assets/knowledge/key.png){:height="20px"}

*signature를 확인하는 과정이다.*  
![validate]({{site.url}}/assets/knowledge/validate.png)

apache-maven-3.5.2-bin.zip이 apache 개발자인 "Stephen Connolly"씨에게 검증을 받은 파일이라는 것을 나타낸다.
primary key fingerprint는 asc파일을 해독한 public key를 사람이 보기 좋도록 나타낸 폼이다. 

![public]({{site.url}}/assets/knowledge/public.png)
위 캡처는 .gnupg/trustdb.gpg의 내용인데, 보면 "Stephen Connolly"씨의 "C7CA..."가 fingerprint에 들어있는 것을 볼 수 있다.

추가로, PGP에는 web of trust를 통해서 public key의 "신뢰도"를 정해준다. 이에 대해서는 [정리된 글](https://blog.naver.com/wnrjsxo/221161639001), [정리된 글2](https://www.gnupg.org/gph/en/manual/x334.html)이 있는데 이해가 잘 안간다....
