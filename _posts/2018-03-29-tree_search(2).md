---
layout: page
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

![admissible]({{site.url}}/assets/artificial/admissible.png){:height="300px" .center-image}  

A, B 노드에서 heuristic값을 바꿔보면서 어떤결과가 나오는지 확인해 보자. h(A) < h(B)라면 당연히 optimal한 값을 찾을 것이기에, 반대의 상황만 확인을 해보자.  
1. h(A) = 8, h(B) = 7 (Not Admissible)  
 f(A) = 1+8, f(B) = 1+7 이고 f(A) > f(B)이므로 B노드가 선택된다. 현재 fringe(=OPEN)에는 A, Goal 노드가 들어있고, f(A) = 1+8, f(Goal) = 5+0이므로 최종적으로 Start → B → Goal 이 선택되고 이는 optimal한 결과가 아니다.

2. h(A) = 2, h(B) = 1 (Admissible)  
 f(A) = 1+2, f(B) = 1+1 이고 f(A) > f(B)이므로 앞선 예시와 같이 B노드가 선택된다. fringe에는 A, Goal 노드가 들어있고, f(A) = 1+2, f(Goal) = 5+0 이다. 따라서, f(A)가 다시 선택이 되고 Start → A → Goal이 답으로 결정된다.

수학적인 증명도 찾으면 있겠지만... 느낌적인 느낌을 알 수 있다.

---

**4. Heuristic 함수 만드는 법? Relax!**

문제의 조건을 제거해 나가는 방법을 통해서 heuristic 함수를 만들어낸다. 예를 들어 장애물을 피해 목표에 도달해야하는 문제가 있다면, 장애물을 통과해 지나가도 된다고 가정하여 함수를 만들 수 있다.

---

### 길찾기 예제 (Greedy Best Search vs A* search)

{% highlight C++ %}
// 출발지에서 도착지까지 최적의 루트를 찾기
// 단, 도착지가 여러개. 가장 가까운 도착지를 선택함
// 
// 입력 : N, M (Map size), Map
// 1 : 길, 2 : 벽, 3 : 출발점, 4 : 도착점
12 11
1 1 1 1 1 1 1 1 1 1 1
3 2 2 2 2 2 2 1 2 2 1
1 2 1 1 1 1 2 1 2 1 1
1 2 1 2 2 2 2 2 2 2 1
1 2 1 2 1 1 2 1 1 2 1
1 2 2 2 2 1 2 2 1 2 1
1 2 1 1 2 2 2 1 1 4 1
1 2 1 2 2 2 2 2 2 1 1
1 2 2 2 1 2 2 1 2 2 1
1 1 1 2 1 1 1 1 1 2 4
1 2 1 2 2 2 2 2 2 2 1
1 1 1 4 1 1 1 1 1 1 1
{% endhighlight %}

  
**공통 코드**
{% highlight C++ linenos %}
#include <iostream>
#include <vector>
#include <queue>

#define ABS(x,y) (x-y > 0 ? x-y : y-x)

using namespace std;

int col, row;
int map[500][500];
int visited[500][500];

typedef struct p{
	int x;
	int y;
	int a;
	vector<p> route;
	p() : x(), y(), a() {}
	p(int x, int y) : x(x), y(y), a(0) {}
	p(int x, int y, int a) : x(x), y(y), a(a){}
}Point;

struct cmp{
	bool operator()(Point a, Point b){
		return a.a > b.a;
	}
};

Point start;
vector<Point> finish;

int length = 0, totalCnt = 0;

// n w s e
int dirX[4] = { 0, -1, 0, 1 };
int dirY[4] = { -1, 0, 1, 0 };

void printMap();
void search(Point p);
int minDistance(Point cur, vector<Point> arr);

int main(void) {

	cin >> col >> row;

	// get input
	for (int c = 0; c < col; c++) {
		for (int r = 0; r < row; r++) {
			cin >> map[c][r];
			if (map[c][r] == 3) {
				start = Point(Point(r, c));
			}
			if (map[c][r] == 4) {
				finish.push_back(Point(r, c));
			}
		}
	}

	search(start);


	cout << "length : " << length << endl;
	cout << "time : " << totalCnt << endl;

	return 0;
}

// using BFS
void search(Point p){

	priority_queue<Point, vector<Point>, cmp> pq;
	pq.push(p);

	while(pq.size() != 0){

		Point q = pq.top();
		pq.pop();

		// Add "route", check "visited"
		vector<Point> r = q.route;
		r.push_back(Point(q.x,q.y));
		visited[q.y][q.x] = 1;
		totalCnt++;

		// FOUND :D
		if(map[q.y][q.x] == 4){
			for(int i=1; i<q.route.size(); i++){
				map[q.route[i].y][q.route[i].x] = 5;
			}

			printMap();
			length = q.route.size()-1;
			return;
		}

		for (int i=0; i<4; i++){
			Point next = Point(q.x+dirX[i], q.y+dirY[i]);
			
			// check bound condition
			if (next.x < 0 || next.x >= row || next.y < 0 || next.y >= col) {
				continue;
			}
			// NOT to visit the point which has already been visited
			else if(visited[next.y][next.x]){
				continue;
			}

			// Searching candidate
			else if (map[next.y][next.x] == 2 || map[next.y][next.x] == 4) {
				
				Point n = Point(next.x, next.y, minDistance(next, finish));
				n.route = r;

				pq.push(n);
			}
			else{
				continue;
			}
		}
			

	}

}
{% endhighlight %}
  
---

**Greedy Best Search**
{% highlight C++ linenos %}
int minDistance(Point cur, vector<Point> arr){

	int min = 987654321;
	for(int i=0; i<arr.size(); i++){
		int diff = ABS(arr[i].x, cur.x) + ABS(arr[i].y, cur.y);
		if (min  > diff) {
			min = diff;
		}
	}
	return min;

}
{% endhighlight %}

---
  
**A* Search**
{% highlight C++ linenos %}
int minDistance(Point cur, vector<Point> arr){

	int min = 987654321;

	int passing = ABS(start.x, cur.x) + ABS(start.y, cur.y);

	for(int i=0; i<arr.size(); i++){
		int diff = ABS(arr[i].x, cur.x) + ABS(arr[i].y, cur.y) + passing;
		if (min  > diff) {
			min = diff;
		}
	}
	return min;

}
{% endhighlight %}

---

**결론**

두 알고리즘 모두 목적지까지의 Manhattan Distance를 Heuristic으로 사용하였다. Length는 찾은 루트의 길이, Time은 총 탐색횟수를 의미한다.

`Greedy Best` : Length = 12, Time = 14  
`A*` : Length = 12, Time = 21

A*가 더 많은 탐색횟수를 보인다.
