---
layout: post
title:  "Tree Search Problem - informed Search"
date:   2018-03-29 00:00:00
category: 인공지능
tags: informed heuristic
---

인공지능 수업내용의 정리 : informed search

<!-- more -->

<br>

---

### Uninformed Search?

Heuristic을 사용하는 search방법이다. Greedy best-first search와 A* search등이 있다.

---

**1. Greedy best-first search**  

`f(n) = h(n)` 인 search 방법이다. h(n)이 작은 node부터 무작정 따라간다. 

>
**Complete** : No..  
**Optimal** : No..  
**Time Complexity** : O(b^m)  
**Space Complexity** : O(b^m)  
>

(m = maximum depth of search space)

---

**2. A* Search**  

`f(n) = g(n) + h(n)` 의 요소를 모두 사용하는 search이다. heuristic 함수에 따라 조금은 갈릴 수도 있지만, 성능이 가장 좋은 것으로 알려져있다. 참고로 스타크래프트에서 유닛의 움직임이 이 알고리즘으로 구현되어 있다고 한다.

>
**Complete** : Yes  
**Optimal** : Yes  
**Complexity** : Exponential(Worst)  
>

---

**3. Admissible heuristic**  

* h'(n)는 목표까지의 실제 cost라고 할 때, `h(n) ≤ h'(n)이어야 한다`.
* A* search의 h(n)이 Admissible heuristic인 경우, 결과는 항상 optimal하다.
* admissable한 두 함수 h1, h2에 대하여, 모든 n에 대해 `h2(n) ≥ h1(n)`를 만족하면, `h2 dominate h1`이다. 이는, h2가 search에 더 좋은 효율을 보임을 뜻한다. 즉, 실제와 가까운 heuristic일 수록 좋다.

그렇다면 `어떻게 admissible heuristic이 optimal하다고 보장할 수 있을까?` 다음의 [출처](https://cs.stackexchange.com/questions/16065/how-does-an-admissible-heuristic-ensure-an-optimal-solution?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)를 참조하였다. 아래와 같은 그래프가 있다고 하자. 각 간선에 적힌 숫자는 노드와 노드 사이의 cost를 의미한다.

![admissible]({{site.url}}/asset/180329/admissible.png){:height="300px" .center-image}  

A, B 노드에서 heuristic값을 바꿔보면서 어떤결과가 나오는지 확인해 보자. h(A) < h(B)라면 당연히 optimal한 값을 찾을 것이기에, 반대의 상황만 확인을 해보자.  
1. h(A) = 8, h(B) = 7 (Not Admissible)  
 f(A) = 1+8, f(B) = 1+7 이고 f(A) > f(B)이므로 B노드가 선택된다. 현재 fringe(=OPEN)에는 A, Goal 노드가 들어있고, f(A) = 1+8, f(Goal) = 5+0이므로 최종적으로 Start → B → Goal 이 선택되고 이는 optimal한 결과가 아니다.

2. h(A) = 2, h(B) = 1 (Admissible)  
 f(A) = 1+2, f(B) = 1+1 이고 f(A) > f(B)이므로 앞선 예시와 같이 B노드가 선택된다. fringe에는 A, Goal 노드가 들어있고, f(A) = 1+2, f(Goal) = 5+0 이다. 따라서, f(A)가 다시 선택이 되고 Start → A → Goal이 답으로 결정된다.

수학적인 증명도 찾으면 있겠지만... 느낌적인 느낌을 알 수 있다.

---

**4. Heuristic 함수 만드는 법? Relax!**

문제의 조건을 제거해 나가는 방법을 통해서 heuristic 함수를 만들어낸다. 예를 들어 장애물을 피해 목표에 도달해야하는 문제가 있다면, 장애물을 통과해 지나가도 된다고 가정하여 함수를 만들 수 있다.