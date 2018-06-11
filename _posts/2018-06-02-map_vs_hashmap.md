---
layout: page
title:  "map vs hash map"
date:   2018-06-02 00:00:00
category: algorithm
tags: map hash-map
---

우연찮게 c++의 container인 map의 내부 implementation이 red black tree로 되어있다는 사실을 알게되었다. map은 당연히 hash로 되어있는게 아닌가? 라고 생각하고 있던 차라.. 적잖이 충격이었다. 찾아보니, hash로 구현해놓기도 한다. Java의 경우에는 map이라는 interface를 가지고 이를 implement하는 방법에 따라 treeMap, hashMap 등등으로 나뉘더라. tree와 hash로 구현되어 있는 경우에 어떤 차이가 있는 지 공부를 해보았다. 

---

## tree map

Red Black Tree(이하 RB tree)는 기본적으로 binary tree이다. binary tree는 insert되는 값들의 순서에 따라 skew된 tree구조를 가질 수 있다. skew된 tree는 노드를 search할 때 문제가 되는데, 최악의 경우 $$O(n)$$이 된다. 이 때문에 tree 높이의 balance 유지시켜주는 자료구조가 고안이 되었고, 그 결과물이 [AVL tree](https://en.wikipedia.org/wiki/AVL_tree)와 [RB tree](https://en.wikipedia.org/wiki/Red–black_tree)이다. c++에서 `std::map`은 이 중 RB tree를 사용한다.


**1. RB tree는 어떤 성능을 내는가?**
>
search : $$O(logN)$$  
insert/delete :  $$O(logN)$$
>

**2. AVL tree와 비교해서 이점이 있나?**  
생각을 해보면, AVL tree도 같은 complexity를 가지고 있다. 왜 굳이 RB tree를 사용할까? 다음 [stackoverflow](https://stackoverflow.com/questions/16257761/difference-between-red-black-trees-and-avl-trees?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)를 보고 이것저것 많은 것을 배울 수 있었다.

AVL tree는 RB tree에 비해 더욱 까다롭게 balance를 맞추는 tree이다. Big-O notation을 쓸 때, 세세한 것은 다 쳐버리고 표현하기 때문에 두 tree모두 search에 $$O(logN)$$의 complexity를 가진다고 생각했다. 하지만, 엄밀히는 다르다. AVL tree는 최대 $$1.44log(N+2)$$의 height을 가지고, RB tree는 $$2log(N+1)$$의 height을 가진다. 따라서 search에 있어서는 AVL tree가 더 빠르다.

두 tree 모두 balance를 유지하기 위해 rotate나 coloring 같은 작업들을 해준다. Big-O notation으로 보면 insert/delete에 둘다 $$log(N)$$이 들지만, insert/delete를 위해 수행하는 rotate 작업의 수는 차이가 난다. AVL tree가 더욱 balance를 까다롭게 유지하기 때문에 O(logN)rotate를 수행하고, RB tree는 O(1) rotate를 수행한다.

따라서, search가 주요 작업인 곳에서는 AVL tree를 쓰는게 좋고, insert/delete를 주로 쓰는 곳에서는 RB tree가 좋다.

**3. RB tree와 cache이슈**  
RB tree의 rotate횟수가 적은 것이 insert/delete를 보다 빠르게 하는 요인이었다. rotate를 한다는 것은 메모리에 쓴다는 것을 의미하는데, 결국 cache에 얼마나 hit가 되냐가 성능을 크게 좌우한다. 그런데, RB tree의 경우도 결국 tree의 height이 깊어지다보면 insert/delete시에 cache miss의 비율이 높아지고 성능이 느려지게 된다.

STL을 만든 사람인 Alexander Stepanov는 [인터뷰](https://interviews.slashdot.org/story/15/01/19/159242/interviews-alexander-stepanov-and-daniel-e-rose-answer-your-questions)에서 다음과 같이 말했다.
>
One of the biggest changes since then has been the growth of caches. Cache misses are very costly, so locality of reference is much more important now. Node-based data structures, which have low locality of reference, make much less sense. If I were designing STL today, I would have a different set of containers. For example, an in-memory B*-tree is a far better choice than a red-black tree for implementing an associative container. - Alexander Stepanov
>

지금 다시 STL을 만든다면, B* tree를 고려하겠다는 내용이다. 왜냐하면, B* tree는 노드에 element를 인접하게 저장하기 때문에 더욱 cache friendly하다는 것이 그 이유이다.

추가로, Alex는 같은 이유로 엄청 큰 N이 아니라면, `std::vector`와 `std::binary_search`를 쓰는 것이 `std::map`보다 빠를 것이라고 했다.

---

## Hash map

map이 python에서는 dictionary에 대응된다고 생각했다. 그래서 python에서는 dictionary가 어떻게 구현되었는지 찾아봤다. python 공식 [document](https://docs.python.org/2/faq/design.html#how-are-dictionaries-implemented)를 보면 resizable hash table을 이용해 구현되어 있다고 되어있다.

원래 c++의 stl에는 hash map이 없었다. 하도 많이들 쓰니까 hash_map이란 이름으로 야매(?)로 제공을 해오다가, c++11에 이르러서 unordered_map이란 이름으로 정식 stl이 되었다.

**1. Hash collision**  
hash의 get은 일반적으로 time complexity가 $$O(1)$$로 알려져 있지만, 사실 어느정도 item이 들어가다 보면 그런 성능은 기대하기가 힘들다.

java의 hashMap을 설명한 [NaverD2의 글](https://d2.naver.com/helloworld/831311)을 보면, java에서는 hash collision이 일어났을 때, `seperate chaining`이란 방법을 쓴다. item의 개수가 적을 때는 linked list를 이용하여 관리하여 $$O(N)$$ 이지만, item 개수가 많으면 RB tree를 이용하여 item을 관리하여 $$O(logN)$$의 성능을 낸다.


**2. Hash Table**  
Hash bucket의 개수의 경우, key-value쌍이 일정 개수 이상 되면 그 수를 늘리는 방법을 사용한다. 예를 들어, 현재 데이터 개수가 bucket의 총 개수의 75%만큼 찼을 때 bucket의 개수를 2배로 늘리는 방법을 정해줄 수 있다.

---


참고)  
http://gpgstudy.com/forum/viewtopic.php?t=2937  
http://zeddios.tistory.com/237  
https://stackoverflow.com/questions/16257761/difference-between-red-black-trees-and-avl-trees?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa  
https://www.cs.usfca.edu/~galles/visualization/RedBlack.html  
https://d2.naver.com/helloworld/831311  


