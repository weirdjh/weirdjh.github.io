---
layout: post
title:  "Priority Queue"
date:   2018-03-17 00:00:00
category: algorithm
tags: PriorityQueue Heap dijkstra
---

인공지능 수업에서 uniform cost search에서 우선순위 큐에 대한 이야기가 나왔다. 기억이 잘 나지 않았다. heap부터 priority queue를 구현하고, dijkstra까지 구현을 해보면서 복습하자.
<!-- more -->

<br>

---
### Heap 구현  

{% highlight python linenos %}
class min_heap:
	def __init__(self,arr):
		self.arr = arr

	# Bottom-Up 방식의 heapify
	def heapify(self, i):
		N = len(self.arr)
		left = i * 2 + 1
		right = i * 2 + 2
		smallest = i

		if left < N and self.arr[left] < self.arr[i]:
			smallest = left
		if right < N and self.arr[right] < self.arr[smallest]:
			smallest = right

		if i != smallest:
			self.arr[i], self.arr[smallest] = self.arr[smallest], self.arr[i]
			self.heapify(smallest)

	# Bottom-Up이기 때문에 leaf node는 heapify를 해줄 필요가 없다.
	def min_build(self):
		for i in range(int(len(self.arr) / 2))[::-1]:
			self.heapify(i)
		return self.arr

	def getArr(self):
		return self.arr

{% endhighlight %}
Top-down 방식, Bottom-up 방식 두가지도 heapify를 구현할 수 있다. 두가지 방식모두 O(nlogn) 시간복잡도를 가진다. 성능상 Bottom-up이 약간은 좋다고 하는데, 관심이 생긴다면 [이곳](http://www.cs.umd.edu/~meesh/351/mount/lectures/lect14-heapsort-analysis-part.pdf)을 참고해보자.

<br>

---
### Priority Queue 구현  


{% highlight python linenos %}
class priority_queue():
	def __init__(self,arr):
		self.arr = arr

	def insert(self, N):
		self.arr.append(N)
		idx = len(self.arr)-1

		# '//' 연산은 나눗셈 후 floor까지 해준다.
		while idx != 0:
			if self.arr[idx] < self.arr[(idx-1)//2]:
				self.arr[idx], self.arr[(idx-1)//2] = self.arr[(idx-1)//2)], self.arr[idx]
				idx = (idx-1) / 2
			else:
				break
		
	def pop(self):
		ret = self.arr[0]

		self.arr[0] =  self.arr[-1]
		self.arr.pop()

		heap = min_heap(self.arr)
		self.arr = heap.min_build()
		return ret

	def top(self):
		return self.arr[0]

	def empty(self):
		return not self.arr

	def getPQ(self):
		print(self.arr)

{% endhighlight %}
insert의 경우 부모를 계속 찾아가면서 swap을 해준다.  
pop의 경우 첫째 원소를 빼고, 마지막 원소가 처음원소 자리에 위치하게 된다. 그 후 heap을 다시 만들어준다.


<br>

---
### Djikstra 구현  

{% highlight python linenos %}
INTMAX = 987654321

def djikstra(start, graph):
	dist = defaultdict(dict)
	for i in range(1,6+1):
		dist[i] = INTMAX
	dist[1] = 0	

	pq = priority_queue([])

	# first element
	pq.insert((0,1))

	# BFS와 비슷한 진행을 한다.
	while not pq.empty():
		cost = pq.top()[0]
		loc = pq.top()[1]

		pq.pop()

		# 이미 dist에 저장된 값이 cost보다 크면,
		# 굳이 연산을 진행할 필요가 없다.
		if cost > dist[loc]:
			continue

		for edge in graph[loc]:
			next = edge
			next_cost = cost + graph[loc][edge]
			if dist[next] > next_cost:
				dist[next] = next_cost
				pq.insert((next_cost, next))
	
	return dist
{% endhighlight %}
1. python에서는 `tuple간 비교`가 된다. (1,2) < (2,3) , (1,2) < (1,4)
2. `defaultdict`은 엄청나게 편리한 자료형이다. 매우 손쉽게 adjacency list를 구현할 수 있었다. graph는 start : {end: distance} 의 형태로 구성했다. `graph = defaultdict(dict)`, `graph[s][e] = d`

3. 최단 거리를 위해 선형탐색을 이용하면 O(V^2)를 갖지만, priority queue를 이용하면 `O(ElogV)`까지 줄어든다. 힙 정렬시간은 O(nlogn)이므로, dijkstra최악의 경우 모든 edge를 queue에 넣게된다면 O(ElogE)가 된다. E <= V^2이므로 O(logE) <= O(logV)이고, 따라서 O(ElogV)의 시간복잡도를 가진다.

<br>

---
### Uniform Cost Search

구현을 해놓아라.

<br>

---
### Uniform Cost Search와의 차이점?

Uniform Cost Search는 현재의 상태를 지나온 노드들의 path cost를 합한 값으로 한다. 현재까지 진행한 값이 가장 작은 것을 골라서 진행하게 되는데, priority queue를 이용해 구현한 dijkstra와 매우 닮아있다. 실제로 stack overflow에서는 [이에 대한 질문](https://stackoverflow.com/questions/12806452/whats-the-difference-between-uniform-cost-search-and-dijkstras-algorithm)이 있었다.

둘은 기본적으로 같은 개념을 공유한 알고리즘이고, 차이점은 다음과 같다.
1. Dijkstra는 root에서 모든 노드들까지의 최소 path가 모두 구해진다. 처음부터 priority queue가 filled 상태이다. priority queue가 비어져야 끝난다.
2. UCS는 root에서 goal 노드까지의 path를 구한다. priority queue를 처음부터 꽉꽉채워쓰지 않는다. goal을 찾으면 종료한다.

읽고보니 별 내용이 아닌데... 그냥 그렇다...
