---
layout: post
title:  "개행 문자"
date:   2018-03-17 00:00:00
category: knowledge
tags: LF CR CRLF
---

mac에서 작성한 txt파일을 윈도우에서 확인하면 이상한 문자가 많이 들어가 있는 것을 볼 수 있다. 예전에 Atom에서 아래 상태 표시줄에 CR, LF, CRLF를 선택하는 것을 보고 뭐지?라고 생각한 적이 있었다. 겸사겸사 공부를 해보고, 왜 이런 문제가 발생하는지 알아봐야겠다고 생각을 했다. 

<!-- more -->

<br>

---
### LF , CR , CRLF

**Line Feed(LF)**  

ASCII or Unicode : 16진수로 0A (`\n`)
기능 : cursor의 위치를 다음 행으로 내린다.


**Carriage Return(CR)**  

ASCII or Unicode : 16진수로 0D (`\r`) ... `^M`으로 보일 수 도 있다.
기능 : cursor의 위치를 동일 line의 제일 첫 위치로 보낸다.

**CRLF**  

애초에 CR과 LF의 개념은 타자기(typewriter)를 쓰던 시절에서부터 내려온 개념이다. 타자기에서 새로운 행으로 넘어가기 위해서, 먼저 같은 행의 처음으로 cursor를 옮기고(CR) 다음 행으로 개행(LF)을 하게 된다. 컴퓨터에서도 똑같은 개념을 가져와서 개행을 CRLF로 쓰기 시작했다.

참고로, 타자기에서 CR을 LF보다 먼저 쓰는 이유는 CR이 매우 오래 걸렸기 때문이다. CR을 통해 cursor가 움직이는 사이에, LF 명령이 타자기로 들어가서 시간을 좀 더 효율적으로 사용할 수 있었다고 한다...

사실, 컴퓨터 텍스트 파일에서 행갈이는 단순한 작업이기 때문에 CR,LF를 동시에 쓸 필요가 없다. 지금에서는 MS에서만 CRLF를 동시에 사용해서 개행을 하고 있다.

Word나 HWP 문서에서 보는 `↵` 기호가 CRLF를 뜻하는 기호이다.

<br>

---

### 왜 OS마다 각각 다른 개행문자를 사용할까?
>
DOS/Window : CR + LF  
Unix/Linux : LF  
MAC(ver9) : CR  
MAC OS X : LF
>
위와 같이 각각 달느 운영체제에서 다른 개행문자를 사용한다.

왜 각각의 운영체제는 다른 개행문자를 사용하게 되었을까? 이에 대한 대답은 OS가 처음 설계되었을 때로 거슬러올라가는 듯 하다.
[이 곳](https://unix.stackexchange.com/questions/411811/why-does-linux-use-lf-as-the-newline-character)에 질문에 대한 답이 있다.


<br>

---

참고 : http://en.wikipedia.org/wiki/Newline
