---
layout: post
title:  "Tree Search Problem - Uninformed Search"
date:   2018-03-28 00:00:00
category: 인공지능
tags: uninformed bfs dfs depth-limited iterative-deepening
---

인공지능 수업내용의 정리 : uninformed search


<!-- more -->

<br>

---

### Tree Search Problem?

Graph, Tree와 같은 자료구조로 구성되는 환경에서 목적지를 찾아가는 문제이다.
Initial state에서 시작해서, 이미 탐색한 state의 successor를 만들어가면서 탐색을 진행하며, goal state에 도달하는 것이 목표이다.

이 아이디어를 수식으로 옮겨서 표현하면, `f(n) = g(n) + h(n)` 으로 표현이 된다.
>
**f(n)**은 evaluation function으로, 각각 노드에서의 **desirability**를 뜻한다.  
**g(n)**은 현재까지 지나온 행동들의 cost값의 합이다.  
**h(n)**은 heuristic function으로, 문제에서 추가적인 정보(problem-specific knowledge)를 통해 얻을 수 있는 값이다.  
>

h(n)의 사용 유무에 따라 크게 Uninformed Search와 Informed Search로 나뉘고, g(n)을 어떤식으로 구하냐에 따라 또다시 세부적으로 분류가 나뉜다.

<br>

---

### Uninformed Search?

h(n)을 사용하지 않는 search. 즉, 탐색에 대한 정보가 uninformed인 탐색을 말한다. BFS, DFS, Iterative deepening Depth-first, UCS(Uniform Cost Search) 등의 방법들이 있다. 

---

**1. BFS(Breadth-First Search)**  

`g(n)이 트리의 depth값으로 고정`된 방법이다. Queue를 이용한 FIFO방식이며, Shallowest unexpanded node로 부터 탐색을 진행한다.	

>  
**Complete** : yes  
**Optimal** : yes  
**Time Complexity** : O(b^d)  
**Space Complexity** : O(b^d)  
>

(b = branching factor, d = depth of least-cost solution)
(Complete는 답을 찾는지 여부, Optimal은 찾은 답이 최적의 답인지 여부를 나타낸다.)

---

**2. DFS(Depth-First Search)**  

`g(n)이 0으로 고정`된 방법이다. Stack을 이용한 LIFO방식이며, 한쪽 방향으로만 우선적으로 탐색을 진행한다.

>	
**Complete** : 유한 상태의 그래프(dead-end가 있는 경우)에서만 complete가 가능하다.  
**Optimal** : X, 중간에 답을 만나면 종료하므로, 얻어진 결과가 최단 경로라는 보장이 없다.  
**Time Complexity** : O(b^m)  
**Space Complexity** : O(bm), 현 경로상의 노드들만을 기억하므로, 저장공간이 linear하다.  
>

(b = branching factor, m = maximum depth of the state space)
		
---

**3. BFS vs DFS 성능 비교**  

* Time Complexity의 경우 동일하다고 말할 수 있으나, 최악의 경우를 가정하면 BFS가 약 b/(b-1) 만큼 더 빠르다. 하지만 DFS가 정상적으로 종료되는 환경이라면, 일반적으로 DFS가 더 빠르다.
* BFS는 goal이 깊이 있지 않은 경우에 유리하다.
* DFS가 메모리상 훨씬 유리하다.

---

**4. IDS(Iterative Deepening Search)**  

DFS가 정답을 찾지 못한 채 트리의 심연으로 빠지는 것을 방지하기 위해서, 탐색의 depth limit을 L로 정해두는 방법을 DFS with depth-limit이라고 한다. L=1 일때 부터 시작해서 L의 값을 늘려주며 순차적으로 탐색을 진행한다. 정답을 찾으면 종료한다. 트리가 얼마나 깊은지 알지 못할 때 좋다.

>
**Complete** : yes  
**Optimal** : yes  
**Time Complexity** : O(b^d), 최악의 경우로 갈수록, BFS와 같은 시간복잡도를 갖게 된다.  
**Space Complexity** : O(bd)  
>

(b = branching factor, d = depth of least-cost solution)

---

**5. IDS는 비효율적이지 않은가?**  

IDS가 같은 search를 계속 반복하기 때문에 효율성에서 매우 떨어지지않을까? 생각할 수 있다.


b = 10, d = 5일 때, BFS와 IDS의 Time Complexity를 계산해보면,  
BFS의 경우, b + b^2 + b^3 + ... + b^d = 111,110  
IDS의 경우, b + (b + b^2) + ... + (b + b^2 + ... + b^d) = 123,450  
으로 사실상 차이가 그렇게 크지 않다.

---

**6. UCS(Uniform Cost Search)**  

`g(n)을 start state에서 부터 node n까지의 cost`로 정한다. g(n)이 가장 작은 node부터 탐색을 한다. Dijkstra알고리즘과 매우 흡사하다.

또한, `BFS는 UCS에서 모든 edge의 cost가 같은 경우와 같다`. UCS가 좀 더 일반화된 형태라고 할 수 있겠다.

Complete와 Optimal이 보장된다.