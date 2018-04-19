---
layout: post
title:  "Hill Climbing"
date:   2018-04-15 00:00:00
category: 인공지능
tags: hillclimbing
---

인공지능 수업내용의 정리 : hill climbing


<!-- more -->

<br>

---

### Local Search란?

A* 알고리즘이라는 매우 빠른 속도로 탐색을 하는 알고리즘이 있다. 하지만 좋은 휴리스틱이 정의되어 있음을 전제로 하고, 어떤 문제의 search space가 매우 거대한 경우 A* 알고리즘 마저도 문제의 해답을 찾는데 혀를 내두르게 된다. 이를 위해 모든 경우를 탐색하지 않고 주변 노드만을 탐색해 나가면서 해답을 찾는 방법(local search)을 쓰게되었다. 비록 optimal한 답을 찾는다는 보장이 되어있지 않다. 하지만, 현실의 문제는 [TSP](https://en.wikipedia.org/wiki/Travelling_salesman_problem)와 같이 매우 복잡하기에... 계산수를 줄이고 최대한 답에 근사한 답을 찾아가는 것도 의미가 있다.


---

### Hill Climbing Search란?

local search의 한 종류인데, greedy하게 search를 진행한다. 정의된 heuristic함수에 의해서 더 좋은 값이 나오지 않으면,local maximum에 도착하면 종료한다. local maxumum은 global maximum이 아닐 수 있다(= optimal하지 않다).

![local]({{site.url}}/asset/180415/local.png){:height="250px" .center-image}  

위 그림을 보면, optimal한 답을 찾기 위해 처음에 어디서 시작해야 할 지를 정하는 것이 중요해 보인다. 하지만 사실상 어디서 시작하는게 좋은 것인지 알기는 쉽지 않으므로, 랜덤으로 처음 시작 상태를 주고 local maximum을 찾아가는 진행을 여러번 하는 방법을 통해 최대한 globla maximum에 근사하도록 하는 방법 등이 있다.

---

### 예제) N-Queens Problem

너무나도 유명한 문제인데, N*N board에 N개의 Queen을 서로 공격하지 못하도록 배치하는 문제이다. 심심해서 Hill Climbing을 통해 이 문제를 해결하도록 구현을 해보았다.

{% highlight python %}
import random

# # of pairs of Queens that're attacking each other
def heuristic(queens):
	
	conflictQueenPair = 0

	for queen in queens:
		x,y = queen[0], queen[1]

		for _queen in queens:
			_x, _y = _queen[0], _queen[1]

			if(conflict(x,y,_x,_y)):
				 conflictQueenPair += 1
				
	return conflictQueenPair

# queen(x,y) <-> queen(_x, _y)
def conflict(x, y, _x, _y) :
	if x == _x and y == _y:
		return False
	elif x == _x or y == _y or x-y == _x-_y or x+y == _x+_y:
		return True
	else:
		return False

# pick one Queen in one Column randomly
def getRandomQueen(n):
	ret = []
	for r in range(n):
		ret.append((r, random.randrange(1,n)))
	return ret

# get next Successor which has minimum heuristic value
def nextSuccesor(queens, n):
	min = 987654321
	for idx in range(n):
		tmp = queens[idx]
		for possibleRow in range(n):
			queens[idx] = (idx, possibleRow)
			h = heuristic(queens)
			if min > h:
				min = h
				succesor = queens[:]
		queens[idx] = tmp

	return succesor

# solve nqueens problem
def solve(queens, n):
	h = heuristic(queens)
	cnt = 0
	
	while(h > 0):
		nextQueens = nextSuccesor(queens, n)
		nh = heuristic(nextQueens)

		if nh == 0:
			return True, cnt, nextQueens

		elif nh < h:
			h = nh
			queens = nextQueens[:]
			cnt += 1
			
		else:
			return False, cnt, nextQueens

	return True, cnt, queens


def printQueen(queens, n):
	for r in range(n):
		for c in range(n):
			find = False
			for queen in queens:
				if c == queen[0] and r == queen[1]:
					find = True
			if find:
				print('X', end=' ')
			else:
				print('O', end=' ')
		print()

if __name__=='__main__':

	solved, solvedCnt = 0, 0
	unSolved, unSolvedCnt = 0, 0
	
	for i in range(1000):
		queens = getRandomQueen(8)
		
		#printQueen(queens, 8)

		isSolved, count, lastQueens = solve(queens, 8)
		
		if isSolved:
			solved += 1
			solvedCnt += count

		else:
			unSolved += 1
			unSolvedCnt += count

	print("solved : %.2f %%, avg cnt : %.2f"% (float(solved)/1000*100, float(solvedCnt)/solved))
	print("unsolved : %.2f %%, avg cnt : %.2f"% (float(unSolved)/1000*100, float(unSolvedCnt)/unSolved))

{% endhighlight %}

--- 

초기 queen들의 위치를 랜덤으로 선택하여 local maximum을 찾아가는 방법을 1000번 반복하였다. 그 결과 다음과 같은 결과를 얻을 수 있었다.  
![result]({{site.url}}/asset/180415/result.png){:height="80px" }  
