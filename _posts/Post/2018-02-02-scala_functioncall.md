---
layout: post
title:  "Call by Value vs Call by Name"
date:   2018-02-02 20:00:00
category: Scala
tags: cbn cbv
---


PL에서 evaluation strategy는, 
> 언제 function call의 인자로 어떤 값이 넘어가는지, 
> 또 언제 평가(evaluate)하는지를 결정하는 방법이다.

C에서 배운 Call by value, Call by reference등이 이 evaluation strategy의 한 종류라고 보면 된다. 
Scala에서는 Call by value, Call by name의 두가지 전략이 사용된다. 예시를 통해 차이점을 알아보겠다.

<!-- more -->

---
loop, CBV, CBN 세 함수를 정의하였다.
{% highlight scala %}
def loop: Int = loop 
def CBV(x:Int, y:Int)		/* Call By Value */
def CBN(x: Int, y:=>Int)	/* Call By Name */
{% endhighlight %}

CBV(1, loop), CBN(1, loop)를 실행하였을때, 결과적으로 CBV는 무한루프가 돌게 된다. 

Call By Value의 경우, 처음 인자로 전달 받았을 때 계산이 되고, Call By Name은 함수 내에서 사용될 때 마다 계산이 되는 방식이다.(인자로 전달 받았을 때는 계산되지 않는다)

---
value를 정의할 때에도 두가지 방식이 사용될 수 있다.
{% highlight scala %}
def loop: Int = loop 
val x = loop		/* Call By Value */
def y = loop		/* Call By Name */
{% endhighlight %}
이 경우 x는 무한루프가 돌게 된다.

---
(+1)
값을 정의할때, val과 var 두가지이 선언 방법이 있다. val로 선언시 그 값은 변경 불가능 하고 var로는 가능하다. 하지만 Scala에서는 한번 선언된 값이 변경되는 것을 권장하지 않고있다.

 (+2)
script언어 혹은 FP언어는 interactive shell 혹은 REPL을 통해서 expression을 쓰고 그에 대한 계산값을 바로바로 확인해 볼 수 있다.
REPL은 Read, Evaluate, Print, Loop 의 앞글자를 딴 것으로, 사용자의 명령을 읽고 분석-계산(eval)해서 결과를 출력해주는 과정을 반복하는 프로그램이라고 보면 된다.

Scala에서는 Scala REPL이 있고, osx의 경우`brew install scala`를 통해 설치 할 수 있다.


