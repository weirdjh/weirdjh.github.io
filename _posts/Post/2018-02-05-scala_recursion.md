---
layout: post
title:  "Recursion"
date:   2018-02-04 20:00:00
category: Scala
tags: 'tail recursion' 
---

### Why Recursion?
scala에서는 recursion 외에도 for/while을 이용한 loop를 지원한다. 그럼에도 recursion의 사용을 장려하는 눈치다. 어떤 장점이 있는 것일까.

<!-- more -->

역시나 [stackoverflow](https://stackoverflow.com/questions/18645936/is-recursion-in-scala-very-necessary)에는 잘 정리된 답변이 있었다.
먼저 recursion의 장점은 다음과 같다.

1. Recursion을 통해 매우 우아한 솔루션을 만들어 낼 수 있음.
2. tail recursion으로 짜였다면, 심지어 컴파일러 입장에서도 매우 효율적임.
3. tail recursion으로 짜였다면, stack overflow의 위험이 없다.
4. List에 접근하는 경우, head + tail을 이용한 접근이 빠르다.
5. Dynamic Programming과 잘 맞는다. (복잡한 문제를 효율적으로 풀기 쉬워짐)

recursion을 잘 쓴다는 전제 하에 장점을 누릴 수 있는 것 같다...

장점은 알겠다만, recursion이 for/while loop를 모두 대체하긴 힘들지 않을까? 그래서인지 swift나 scala같이 함수형 언어를 사용하는 언어에서는 추가로 high-order combinator나 map, filter등을 이용해서 for/while을 상당부분 대체 할 수 있게 한다고 한다. 

<br>

---

### Tail Recusion은 뭔가?

recursion의 클래식한 코드이다.
{% highlight scala %}
/* sum with recursion */
def sum(xs: List[Int]): Int = 
	case Nil => 0
	case x:xs => x+ sum(xs)
{% endhighlight %}


sum(List(1,2,3))을 호출 한다고 하자. 처음 함수가 실행되면 1+sum(List(2,3))이 될 것이다. 이 경우에, sum(List(2,3))의 결과 값이 1과 더해지기 위해 1은 저장되어야한다. 따라서 sum(List(1,2,3))의 호출을 위한 stack frame이 남아있게 된다. 재귀가 많이 일어나는 경우 정말 큰 메모리가 필요할 것이다... 이를 해결하는 개념이 tail recursion이다.

{% highlight scala %}
/* sum with Tail recursion */
def sum(list: List[Int]): Int = {
    @tailrec
    def sumWithAcc(list: List[Int], currentSum: Int): Int = {
        list match {
            case Nil => currentSum
            case x :: xs => sumWithAcc(xs, currentSum + x)
        }
    }
    sumWithAccumulator(list, 0)
}
{% endhighlight %}


`@tailrec annotation`은 정의된 함수가 tail recursion인지 아닌지 확인한다. 만약 아니라면 컴파일에서 오류를 낸다.  
`match`는 패턴 매칭인데, 어떤 데이터든 매칭할 수 있도록 해준다.

위 tail recursion의 경우, sumWithAcc가 recursive하게 call 될 때, 다른 것 없이 자기 자신만 call하게 된다.

  

<br>

---

### 어떻게 만드나?

다음과 같은 로직을 통해, 대부분의 recursion 함수를 tail recursion로 만들 수 있다.

> make_list_tail_sub(1, R) -> [1 / R];  
> make_list_tail_sub(N, R) -> make_list_tail_sub(N-1, [N / R]).  
> 
> make_list_tail(N) -> make_list_tail_sub(N, []).  
> 
> [출처] Tail recursion을 쓰자 / 작성자 솔라리스  

원래 함수의 sub함수를 하나 만들어서, 예외 처리를 해주고 재귀를 진행하는 식의 진행이다.  
  
  
<br>

---

### Exercise : quick sort

{% highlight scala %}
def quickSort(xs: List[Int]): List[Int] = {
    xs match{
      case Nil => Nil
      case h::t => quickSort(t.filter(_<h)) ::: (h :: quickSort(t.filter(_>=h)))
    }
}
{% endhighlight %}
  
`::` 혹은 `:+` : list앞에 원소를 붙임  
`:::` 혹은 `++` : list 두 개를 붙임  

<br>

---

### Exercise: Hanoi

{% highlight scala %}
def hanoi(plate: Int): Int = {
	def move(src: Int, tmp: Int, dst: Int, plate: Int): Int = {
	    if (plate == 1) 1
	    else move(src,dst,tmp,plate-1) + move(src,tmp,dst,1) + move(tmp,src,dst,plate-1)
    }
    move(1,2,3,plate)
  }
{% endhighlight %}

사실 quick sort나 hanoi나 tail recursion을 만드려면 어떤 예제가 좋을 까 하면서 해본건데, 절대 못만들겠다.... 
