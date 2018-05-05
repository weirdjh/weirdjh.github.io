---
layout: page
title:  "https와 http"
date:   2018-03-17 00:00:00
category: knowledge
tags: https ssl/tls
---

http와 https의 차이는 무엇일까?
<!-- more -->

<br>

---
### HTTP vs HTTPS

`HTTP`는 Hypertext transfer protocol의 약자로, "웹서버 <-> 브라우저"의 문서(하이퍼 텍스트) 전송을 위한 통신 규약이다. 80번 포트를 사용하고, GET, POST등의 메소드 등을 가지고 있다.  

HTTP는 암호화되지 않은 방법으로 데이터를 전송하기 때문에 서버와 클라이언트 사이에 주고 받는 메시지를 감청하는 것이 매우 쉽다. 이에 따라 보안을 위해 웹상에서 종단 간 암호화를 지원하는 글로벌 표준인 HTTPS가 등장했다.  

`HTTPS`의 마지막 S는 Over Secure Socket Layer의 약자로, HTTP 프로토콜에 SSL/TLS 레이어를 입힌 형태이다. 이름의 Secure만 봐도 알 수 있 듯, 암호화를 통해 보안을 신경쓴 프로토콜이다.

[2017년 한 기사](http://www.ddaily.co.kr/news/article.html?no=152720)에 따르면 네이버가 메인 페이지를 https를 적용하지 않아 이슈가 된 적이 있다. 이에 네이버는 메인 페이지에 https를 적용하지 않은 이유는 비로그인된 첫 페이지이기 떄문에 개인정보가 들어가있지 않기 떄문이고, 로그인 창과 검색창에는 https를 적용하고 있다고 밝혔다.  

다른 [블로그](https://item4.github.io/2015-12-19/Naver-and-HTTPS/)의 글을 보면 메인 페이지에 http를 사용하는 것이 여러 보안상 문제가 있을 수 있는 듯 하다.

그렇다면 https를 적용하는 데 있어 어떤 비용이 들기 때문에 https를 사용하지 않은걸까?

<br>

---
### SSL / TLS

`SSL`은 Secure Sockets Layer를 의미하며 이는 웹 브라우저와 엡 서버 사이에 암호화된 통신을 구현하는 글로벌 표준 보안 기술이고, `TLS`(Transport Layer Security) 프로토콜은 SSL 프로토콜에서 발전한 것이다. 두 기술 모두 기밀성(때로는 사생활 보호라고도 함), 데이터 무결성, ID 및 디지털 인증서를 사용한 인증을 제공하는 것을 목적으로 한다(출처 : [IBM Knowledge Center](https://www.ibm.com/support/knowledgecenter/ko/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10630_.htm))  

https에서 서버와 클라이언트는 SSL/TLS handshake를 통해서 session이 연결된다.  

![tls]({{site.url}}/assets/knowledge/tls-handshake.png)  
TLS hasdshake과정을 그림으로 나타낸 것이다. 자세한 과정에 대한 설명은 [오픈튜토리얼](https://opentutorials.org/course/228/4894#signiture)에 설명되어있다 꾸벅..  

간략하게는 정리하면,
1. TCP연결 (Three-way handshake)
2. Client Hello (클라이언트 -> 서버) : 클라이언트에서 가능한 암호화 방식, 클라이언트 측 랜덤 데이터, 세션아이디 보냄
3. Server Hello (서버 -> 클라이언트) : 클라이언트에서 받은 암호화 방식 중 서버측 가능한 암호화 방식 선택하여 전달, 서버 측 랜덤 데이터, 인증서
4. 클라이언트에서는 브라우저에 CA(Certificate authority) 리스트와 각각의 공개키를 가지고 있다. 클라이언트는 서버로 부터 받은 인증서가 CA리스트에 있는 것인지 확인하고, 그에 해당하는 공개키를 이용하여 복호화를 시도한다. 복호화가 성공했다면, 이는 인증서가 CA의 개인키로 암호화 되었임이 보증된다는 뜻이다. 따라서 인증서를 보내온 서버를 신뢰할 수 있게된다.
5. 클라이언트는 양쪽에서 각각만든 랜덤 데이터를 이용해서 세션키(대칭키)를 만든다. CA공개키를 이용하여 암호화하여 서버에 전달하고, 서버에서는 개인키를 이용하여 대칭키를 복호화할 수 있다.
6. 세션이 연결된 후에는 만들어진 세션키를 이용하여 데이터를 암호화하여 주고받는다.
  
따라서, https는 기존 http 프로토콜에 ssl/tls handshake과정이 추가되기 때문에 약간의 성능 저하가 있을 수 있다. 하지만, 구글링을 해보면 이러한 오버헤드는 최근에는 아주 미미한 부분이고 성능에 큰 영향을 주지 않는다는 글이 많다.

<br>

---
### CA(Certificate authority)는 뭐하는 곳일까?

클라이언트가 접속한 서버가 올바른 곳인지를 보장해주는 역할을 하는 민간기업들이 있다. 이런 기업들을 `CA`라고 한다. 신뢰성이 엄격하게 공인된 기업만이 참여할 수 있다. 언젠가 많이 보았던 VeriSign이 높은 점유율을 가진 CA기업이다. SSL/TLS를 통해서 암호화된 통신을 제공하려는 서비스는 CA를 통해서 인증서를 구입하여야한다.  

Cisco, Google, Mozilla, Fastly등의 회사들은 최근에 HTTPS의 빠른 보급을 위해 Let's Encrpt라는 이름의 기관을 만들어서, 무료로 HTTPS 인증서를 보급하고 있다.





<br>

---

참고 :   
https://tech.ssut.me/2017/05/07/https-is-faster-than-http/  
https://opentutorials.org/course/228/4894  
https://www.ibm.com/support/knowledgecenter/ko/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10630_.htm  
