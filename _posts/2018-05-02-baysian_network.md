---
layout: page
title:  "Bayesian Network"
date:   2018-05-02 00:00:00
category: 인공지능
tags: Beysian-Network
---

도둑(Burglary)이 들거나, 지진(Earthquake)이 나는 경우에 우리 집 알람이 작동을 한다.
이웃집에 사는 Mary와 John은 나에게 집 알람이 작동했을 때 전화로 알려준다.
내가 집을 비웠을 때, John이 집 알람이 울렸다고 연락을 줬고, Mary는 연락이 없었다.
이 때, 알람이 울린 원인이 도둑 때문이었을 확률은?

---

### Bayesian Network

[위키](https://ko.wikipedia.org/wiki/베이즈_네트워크)에 따르면, Bayesian Network(BN)란 랜덤 변수의 집합과 방향성 비순환 그래프를 통하여 그 집합을 조건부 독립으로 표현하는 확률의 그래픽 모델이다.

하나의 BN은 각 노드마다 하나의 `조건부 확률 표(CPT; Conditional Probability Table)`을 갖는 `방향성 비순환 그래프(DAG; Directed Acyclic Graph)`로 정의할 수 있다.

노드와 노드를 연결하는 호(arc/edge)는 노드 사이의 관계를 나타내고, 변수의 확률적인 인과관계로 네트워크를 구성하고 조건부 확률 표(CPT)를 가지고 베이즈 정리를 이용하여 결과를 추론할 수 있다. (출처 : [JSYoo](https://www.slideshare.net/JSYoo86/2011-52441421))

---

### 알람 예제

도둑, 지진이 알람에 영향을 줄 수 있고, 알람이 Mary와 John에게 전화를 하도록 한다. CPT가 주어졌다고 치고, 이를 그래프로 그려보면 다음과 같은 DAG와 CPT를 그림으로 그려볼 수 있다.

![bn0]({{site.url}}/assets/artificial/bn0.png){:height="300px" .center-image}  

앞서 Naive Bayesian에서 공부했던 조건부 독립 개념을 이해하면, 이 그래프를 보고 다음 식을 도출할 수 있다.  

$$P(B,E,A,J,M) = P(A \mid B,E) \cdot P(A) \cdot P(J \mid A) \cdot P(M \mid A)$$

<br>

만약 도둑이 들 확률 $$P(B=true)$$를 구하고자 한다면 다음과 같이 구할 수 있다.

\begin{align} P(B=true) & = \\sum_{EAJM}{}P(B=true,E,A,J,M) \\\\ & = \sum_{e \in dom(E)}{}\sum_{a \in dom(A)}{}\sum_{j \in dom(J)}{}\sum_{m \in dom(M)}{}P(B=true,E=e,A=a,J=j,M=m) \end{align}

<br>

이제 John이 연락을 주고, Mary가 연락을 안줬을 때, 알람이 울린 원인이 도둑 때문이었을지를 확인할 시간이다.

\begin{align} P(B=true \mid J=true,M=false) & = \\frac{P(B=true,J=true,M=false)}{P(J=true,M=false)} \\\\ & = \frac{\sum_{EA}{}P(B=true,E,A,J=true,M=false)}{\sum_{BEA}{}P(B,E,A,J=true,M=false)} \\\\ & =0.0495 \end{align}

John이 연락을 줬음에도, 도둑 때문에 알람이 울렸을 가능성은 매우 희박해 보인다.

---

### Compactness

만약, conditional independence를 가정하지 않는다면, True,False의 값을 가질 수 있는 5개의 변수에 대해서 $$2^5$$개의 저장공간이 필요하다. 하지만, BN의 경우 이 예제에서 10개의 저장공간만을 사용하고 있다.

>
일반화해보면, 공간 복잡도는 다음과 같이 줄어든다.  
$$O(2^n) → O(n \cdot 2^k)$$  
(n은 변수의 개수, k는 최대 부모의 개수)
>

BN의 한가지 장점은 복잡한 결합 분포(complete joint distribution)보다 직접적인 의존성(a sparse set of direct dependecies)과 지역 분포(local distribution)를 사람이 이해하는데 직관적이라는 것이다. [-위키](https://ko.wikipedia.org/wiki/베이즈_네트워크)

---

참고)  
https://ko.wikipedia.org/wiki/베이즈_네트워크  
https://www.slideshare.net/JSYoo86/2011-52441421  