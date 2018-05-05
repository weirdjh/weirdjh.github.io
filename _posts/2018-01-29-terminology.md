---
layout: page
title:  "Term 정리"
date:   2018-01-29 20:00:00
category: knowledge
tags: terminology
---

이제는 새로운 용어를 만났을 때 보다 더 꼼꼼하게 정리해야겠다는 생각이 들었다. github 문서를 읽다보면, 자주쓰이고 대충은 무슨 말인지 알겠는데 누군가한테 설명하라고 하면 사실 잘 모르고 있었던 용어들이 많다. 하나하나 짚어나가보자.

<!-- more -->

<hr>

#### `ad-hoc`
   - 기본적으로 on-the-fly의 뜻이다. 문제에 일반적이고, 체계적인 해결책을 제시하는게 아니라, 어떤 특정 케이스를 만족하는 해답을 제시하는 방법이다.
   - pre-defined, encapsulated 보다는 run-time에서 진행되는 과정을 ad-hoc라고 한다.


<hr>

#### `boilerplate`
   - boilerplate code : 마치 무겁고 보기 흉한 보일러처럼 쓸데없이 소스를 길게 만드는 그런 부류의 코드 --> annotation
   - 최소한의 변경으로 재사용이 가능한 저작품 (template과는 조오금 다른 개념 : [참고](https://www.quora.com/What-is-difference-between-a-boilerplate-and-a-template), ex. [html5boilerplate](https://html5boilerplate.com))
   

<hr>

#### `Bootstrap`
   - twitter에서 만든 디자인 툴(css/js)
   - 컴퓨터 전원을 켜거나 리셋했을 떄 필요한 소프트웨어를 로딩함(Booting)
   - 프로그램 installation중, 필요한 package manager나 installer를 업데이트 해주는 것.
    (jekyll의 [bootstrap script](https://github.com/jekyll/jekyll/blob/master/script/bootstrap))
   - (Java) 자바 플랫폼을 구성하는 클래스
   - "with Replacement"를 통해 sampling 해서 training set을 잡는 방버

<hr>

#### `Canonical`
   - Ubuntu 만든 회사 이름
   - 여러 형태로 나타내어질 수 있는 값을 canonical form이라고 함. 디렉토리 주소를 보통 예로 듬. cwd를 표현하기 위해 relative, absolute 주소를 쓸 수 있음. 이중에 한개를 선택해서 표현하게 됨. 이런 개념. 

   ex. canonical url : 웹 페이지에 접근 할 수 있는 주소가 여러개 있는 경우, 검색엔진에게 어떤 주소를 대표로 할 것인지 canonical tag를 통해 알려주는 역할을 함.

<hr>

#### `Cascade`
   - css(cascade style sheet) : 글 [참조](https://www.thoughtco.com/what-does-cascade-mean-3466872)
   사실 별 내용은 아니었다.

<hr>

#### `conservative vs aggressive`
   - compiler 수업에서, 보수적인 typecasting을 conservative라고 표현했다.
   - 반댓말을 aggressive라고 했다. (구글링 결과가 아리송하다..!?)

<hr>

#### `false-positive vs false-negative`
   - Conservative-aggressive와 상통한다.
   - false-positive : 오류가 없는데 있다고 판단하는 것.
   - false-negative : 오류가 있는데 없다고 판단하는 것.
   - 이 두가지 사이에는 항상 **trade-off**가 있을 수 밖에 없다.

<hr>

#### `interpolable`
   - 상호 운용 가능한.  
   ex. Traits are fully **interoperable** only if abstract classes do not contain any implementation code

<hr>

#### `Markup vs Markdown`
   - **Markup** : 문서가 화면에 표시되는 형식을 나타내거나 데이터의 논리적인 구조를 명시하기 위한 규칙들을 정의한 언어의 일종 (HTML, XML).  
   애초에 HTML이 Hyper Text Markup Language이다. (Hyper text는 링크 등을 통해 다른 문서로 넘어갈 수 있는 문서를 말함)
   - **Markdown** : text-to-HTML 변환 툴이다. Markup의 한 라이브러리 정도로 보면된다.

<hr>

#### `Synchronous vs Asynchronous`
   - **Synchronous** : 어떤 루틴을 완전히 끝내고 제어를 반납
   - **Asynchrounous** : 동작이 안 끝났는데도 일단 제어권을 반납하고 자기 할일을 계속함
   - 시스템 프로그래밍에서 다뤘던 내용과 관련해서 [정리가 잘된 글](https://nesoy.github.io/articles/2017-01/Synchronized)


<hr>

#### `Robustness`
   - 에러로 인해 터지지 않게 견고한 프로그램을 짜는 것을 말한다. 더 깐깐하게 에러를 잡으려하면 할 수록 complexity가 증가하므로 robustness <-> scalibility & efficiency 관계에 있다.
   - [Robust Programming](http://nob.cs.ucdavis.edu/bishop/secprog/robust.html)에서는 각종 일어날 수 없다고 생각한 일들이 일어날지도 모르는 상황에 경고하면서, 노하우를 알려주고 있는 듯 하다. 시간 날때... 한번...
   - (Machine learning) 기존 dataset에 noise가 들어가도 stable해야함을 의미한다.

<hr>

#### `Comparable`
   - 썩 나쁘지 않다. (성능 비교)

<hr>

#### `Yet Another`
   - YACC(Yet Another Compiler Compiler)
   - YAML(Yet Another Markup Language)
   - YAHOO(Yet Another Hierarchical, Officious Oracle)
   - 이미 내가 만든거랑 비슷한거 한 사람 엄청 많아서 부끄럽네... 이런 느낌이다.


Event Driven  
call back  
closure  
sandbox  
