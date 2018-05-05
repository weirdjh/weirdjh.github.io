---
layout: page
title:  "oop_polymorphism"
date:   2018-02-06 15:00:00
category: Scala
tags: polymorphism
---


generic function도 polymorphism인가? 헷갈린다. 예전에 oop를 처음 배울때 분명 관련 내용을 공부했는데 제대로 모르는 것이 분명하다. 한번 되짚어보고, scala에서 어떻게 작동하는지 알아보쟝.

<!-- more -->

### 먼저 polymorphism이 뭔가?

> 다른 종류의 data types의 값들을 동일한 interface로 사용할 수 있게 해주는 개념이다.

wikipedia에 따르면, 세가지 종류의 polymorphism이 있다.

<br>

---

### ad-hoc polymorphism
 > functions that can be applied to arguments of different types, but that behave differently depending on the type of the argument to which they are applied


function/operator overloading이 이에 해당한다.  
다음은 programming in scala에 있는 유리수 class 에제이다.

{% highlight scala %}
class Rational (n: Int, d: Int){
  require(d != 0)

  val numer: Int = n
  val denom: Int = d

  def this(n:Int) = this(n,1)

  override def toString = numer + "/" + denom

  def add(that: Rational): Rational =
    new Rational(
      numer * that.denom + that.numer * denom,
      denom * that.denom
    )

  def +(that: Rational) : Rational =
    new Rational(
      numer * that.denom + that.numer * denom,
      denom * that.denom
    )

  def +(i : Int): Rational =
    new Rational(numer + i*denom, denom)

}
{% endhighlight %}

별거 없다..

<br>

---

### parametric polymorphism
> allows a function or a data type to be written generically

사용할 타입들을 [ ]안에 넣어주고 쓰면 generic function을 만들 수 있다. 
{% highlight scala %}
def mapToList[K, V](m: Map[K, V]): List[(K, V)] = m.toList
{% endhighlight %}

{% highlight scala %}
def andThen[A, B, C](f: A => B, g: B => C): A => C = (a: A) => g(f(a))
{% endhighlight %}
<br>
scala는 Rank-1 polymorphism을 지원하는데, [scala-school](https://twitter.github.io/scala_school/ko/type-basics.html)에서는 너무 애매하면 컴파일러가 이해하지 못할 수 있다고 설명한다.

{% highlight scala %}
def foo[A, B](f: A => List[A], b: B) = f(b)
{% endhighlight %}
이 경우에는 컴파일이 되지 않는다. 함수는 자신이 호출 되었을 때 타입이 고정되야하기 떄문에, 우측 함수 f의 타입은 A가 되어야 한다. 그런데 b의 타입은 B이기 떄문이다.

rank-N polymorhism에 대한 설명은 [오픈튜토리얼](https://opentutorials.org/course/2063/11681)에 잘 설명되어있다.
Haskell에서는 Rank-2 이상의 polymorphism을 지원하지만 이를 위해선 따로 명시해야하고, 이를 허용할 경우 함수의 타입을 결정할 수 없다.

scala는 static type을 위해 이를 지원하지 않는 것으로 보이고, 꾸역꾸역 억지로 [scala로 rank-n을 표현하는 글](https://apocalisp.wordpress.com/2010/07/02/higher-rank-polymorphism-in-scala/)을 찾아볼 수 있었다. 읽어보진 않았음.

(공변성이라는 개념도 있던데... 차후에 추가로... )


<br>

---

### subtype polymorphism
> subtyping allows a function to be written to take an object of a certain type T, but also work correctly, if passed an object that belongs to a type S that is a subtype of T

흔히 다형성이라고 부르는 개념이 subtype polymorphism이라고 한다. 한 클래스에서 interface를 만들고, 그 클래스를 상속받은 클래스는 같은 interface로 각각 다르게 사용할 수 있다 이런 내용이다.
  
scala에서는 class외에, abstract class와 trait을 제공한다.
  
다음은 scala공식 docs페이지의 [예제](https://docs.scala-lang.org/ko/tutorials/tour/mixin-class-composition.html.html)이다.
{% highlight scala %}
  abstract class AbsIterator {
    type T
    def hasNext: Boolean
    def next: T
  }

  trait RichIterator extends AbsIterator {
    def foreach(f: T => Unit) { while (hasNext) f(next) }
  }

  class StringIterator(s: String) extends AbsIterator {
    type T = Char
    private  var i = 0
    def hasNext = i < s.length()
    def next = { val ch = s charAt i; i +=1; ch }
  }

  class Iter extends StringIterator("Hello World") with RichIterator
  val iter = new Iter
  iter foreach println
{% endhighlight %}

`abstract class`는 자식 클래스에 메소드 구현을 하도록 강요하는 것이 목적이다. class이기 때문에 오직 하나만 상속 가능하다.  
`trait`은 class에 특성들을 추가로 부여하는 성격이 강해보인다. 여러개를 추가할 수 있다. 

Iter class를 기준으로 첫번째 부모를 super class라고 하고, with로 이어진 것들을 모두 `mixin`이라고 부른다.  

abstract와 class는 약간의 사용벙에서의 차이 외에는 다를 것이 없어보이는 데? 라고 생각이 든다면 어느정도 맞는 애기다.
이미 stackoverflow에는 [열띤 토론](https://stackoverflow.com/questions/1991042/what-is-the-advantage-of-using-abstract-classes-instead-of-traits)이 있었고, abstract의 장점이라면 'java byte code로 컴파일 시 이점이 있다' 이정도가 있었지만 이 마저도 java 8이상으로 넘어가면서 흐물흐물해졌다고 한다.

trait을 되도록 애용하도록 해보자.


trait을 이용한 di, cake pattern ...  
http://jonasboner.com/real-world-scala-dependency-injection-di/

