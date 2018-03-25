---
layout: post
title:  "Scala는 어떤 언어?"
date:   2018-02-01 20:00:00
category: Scala
tags: Scala FP 
---

 새로운 무언가를 배우고 싶었다. 뭘 배울까 하고 찾아봤다. node.js같이 최근에 엄청나게 핫 한 것들을 건드려볼까도 했는데 지금은 스프링 책을 보고있기 때문에 조금 미루기로 했다. 

 그와중에 눈에 들어온 것이 scala 와 kotlin이었다. 두 언어 모두 OOP와 FP를 동시에 지원하는 언어지만, scala는 좀 더 우아하고, kotlin은 좀 더 실용적이라는 뉘앙스의 [글](http://hamait.tistory.com/895?category=79134)을 보고, scala를 공부해보기로 했다.. 대충 훑어보니 ios개발시 썼던 swift와 유사한 개념들이 많이 보였고, 꼼꼼하게 공부해놓는다면 앞으로 FP의 개념들을 차용한 언어를 배울 때 도움이 되겠다 싶었다.

<!-- more -->
<hr>

### Scala : Scalable Language
Scala는 JVM위에서 구동되며, Java와 상호호환이 가능하다. 더 나은 자바를 위해 만들어진 언어이다. Scala의 이름은 Scalable Language의 약자이며, 확장성있는 언어가 언어의 지향점이다.

<hr>

### Scalabilty?
어플리케이션이 사용자의 요구에 맞추기 위해 크기나 용량을 변경해도, 그 기능이 계속해서 잘 동작할 수 있는 능력이다. 

Scala는 확장성있는 언어를 위해 세가지를 고려하여 제작되었다.  

### 1. 객체지향 언어이다.  

다음 표는 절차 지향 언어와 컴퓨터를 비교 한 것이다.

| Imperative Language    | Computer             |
| :--------------------: | :------------------: |
| Mutable variables      | memory cells         |
| Variable dereference   | load instructions    |
| Variable assginments   | store instructions   |
| Control structure      | jumps                |   

<br>
다음에서 볼 수 있 듯, 기존 imperative language는 컴퓨터가 데이터를 읽고 처리하는 방법에 많이 닮아있다. 따라서 프로그램을 word by word로 짜면, 폰-노이만 구조에서 겪는 Bottleneck을 한계로 갖게 된다. 여기서 Scale Up의 한계가 생긴다. 이 문제와 더불어 여러 요구에 의해 디자인 된 것이 OOP 언어이다.
   
여러 OOP언어들에서 볼 수 있었듯, 객체는 모듈화를 통해 큰 프로그램을 적은 도구로 만들 수 있게 해준다.
Scala에서는 모든 것이 객체인 순수한 객체 지향 언어이다. 모든 값들은 객체이고, 모든 operation은 메소드이다. Scala에서 `1+2`를 작성했다면 사실 `(1).+(2)`(1이라는 객체에 "+"메소드를 호출한 것)과 같다.  


  
### 2. 함수형 언어이다.  

함수형 언어는 두가지의 주요한 개념을 가지고 있다.

- 함수는 first-class value이다.
  first-class value는 first-class citizen에서 나온 말로,  

  > Variable에 할당 될 수 있거나,  
  >Parameter값으로 넘겨질 수 있거나,  
  >Return Value로 쓰일 수 있는 값들을 말한다. 
      
  즉, function이 value와 같이 사용된다는 말이다. 이를 통해 함수를 보다 쉽게 정의하고 사용하는 것이 가능해진다.
      
- 프로그램의 연산이 입력값을 변환하지 않고, 출력값에 mapping하는 것이다.
  immutable한 data는 메소드가 side effect를 가지지 않도록 만들어준다.   
    
Haskell같이 모든 함수형 언어의 구문을 제공하지는 않는다만, high-order function, anonymous function, currying등 을 지원한다.
    

### 3. static type 이다. 
- 초기 개발 단계에서 상당수의 에러를 처리할 수 있다.
- 안전한 refactoring을 할 수 있다.
- Type이 그 자체로서 documentation의 역할을 할 수 있다. 
    
static type과 관련되어 장단점을 정리한 [글](https://pchiusano.github.io/2016-09-15/static-vs-dynamic.html)이다. 위와 같은 장점도 있지만, 아무래도 코딩 자체가 깐깐해지는 것은 피할 수 없는 단점이다. 이를 위해서 Scala는Type Inference, Implicit Conversions을 제공한다.





---
공부에 참고할 사이트 :  
[스칼라 학교](https://twitter.github.io/scala_school/ko/) : 트위터에서 기획한 스칼라 강좌이다.  
[스칼라 공식 홈페이지](https://docs.scala-lang.org/) : ..  
[Programming in Scala](http://www.artima.com/pins1ed/a-scalable-language.html) : 언어 제작자가 쓴 책  

> Written with [StackEdit](https://stackedit.io/).
