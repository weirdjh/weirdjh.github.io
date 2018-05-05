---
layout: page
title:  "random in C"
date:   2018-04-22 00:00:00
category: C
tags: random C
---



<!-- more -->

---

### RAND_MAX

[CPlusPlus](http://www.cplusplus.com/reference/cstdlib/RAND_MAX/)에 따르면 놀랍게도 rand()의 최댓값은 RAND_MAX라는 값으로 <cstdlib>에 정의되어있다고 하며, 그 값은 32767이상이라고 되어있다.

32767은 16진수로 0x7FFF이다. random으로 뽑아낼 수 있는 범위는 15비트 이하의 숫자이다.

만약, 10만정도를 넘어가는 랜덤은 rand() % n 으로는 뽑아낼수가 없다는 뜻.  

다른 문제도 있다. 예를들어 rand() % 10000으로 랜덤을 뽑아냈다면, 0~2767은 더 높은 확률로 뽑히게 될 것이다.

---

### Uniformly distributed한 랜덤을 뽑기

double(rand()) / RAND_MAX을 하게 되면 0과 1사이의 고른 분포를 갖게 할 수 있다. 여기에 내가 원하는 랜덤의 최대 크기(RANGE_MAX)를 곱하면 고른 랜덤값을 뽑아낼 수 있다.

>
int(double(rand()) / RAND_MAX * RANGE_MAX)
>
	
그냥 modulo를 통한 방법과 얼마나 차이가 나는 지 실험을 한번 해보았다.

RAND_MAX값을 찍어보니 2147483647가 나온다 (gcc-4.2.1). $$2^{31}-1$$이다. [0,10000)을 1000만번 뽑는 결과에서 각 숫자가 몇 번씩 뽑혔는지를 세고, [0,3647), [3647,10000]의 평균과 [0,10000]의 표준편차를 구해봤다.

결과 :   
1) 그냥 modulo : 1000.331 999.809 31.81  
2) 고른 랜덤값 : 999.953 1000.028 31.105  
  
별로... 차이가 없다... 사실  [0,3647)이 한 개 더 많을 뿐이니, 당연한 결과. 음... 이정도면 그냥 modulo 해서 써도 될 것 같다.

---

### 다양한 난수 생성방법

**1. Middle-square method**   

폰 노이만 씨가 고안한 유사난수법

$$X_{n+1} = (X_n)^2$$의 가운데 a자리를 씀


{% highlight C %}
unsigned long middle_square(void){
	
	// seed : k
	// k의 제곱의 2째자리 부터 4째자리 까지 숫자를 쓰는 예제
	long num1=10000, num2=10;
	long temp, temp1, temp2;

    temp1=pow(k, 2);
    temp=temp1/num1;
    temp2=temp1-temp*num1;
    temp=temp2/num2;
    k=temp;
    
    return temp;
}
{% endhighlight %}

<br/>

**2. Linear Congruential**  

C에서 쓰는 rand()가 구현된 방식이다. $$X_{n+1} = (a X_n + c)\ \text{mod}\ m$$ 와 같은 방법이 쓰인다.

{% highlight C %}
// seed : next
// a = 1103515245
// b = 12345
// m = 2^15
static UINT32 next = 1;

unsigned long middle_square(void){
	next = next * 1103515245 + 12345;
	return (next>>16) & RAND_MAX;
}
{% endhighlight %}

next를 seed로 쓰는데, seed가 같으면 매번 같은 값을 뽑기 떄문에 srand()를 이용하여 새로운 seed를 뽑아줌. srand()의 경우 널리 알려져 있는 것 처럼 1985년 언젠가를 기준으로 현재의 시간을 계산해서 값을 뽑음.

<br/>

**3. Mersenne Twister**  

[wiki](https://en.wikipedia.org/wiki/Mersenne_Twister)를 읽어보고자 했으나 사실 엄두가 잘 안난다. 수학의 영역인 것 같다.. $$2^{19937}-1$$의 주기를 가진다고 한다. 이 말은, $$2^{19937}-1$$번 뽑아야 같은 값이 다시 뽑힌다는 말이다. 

{% highlight C %}
std::random_device rd;
std::mt19937 engine(rd());
std::uniform_int_distribution<int> dist(0,RANGE_MAX);

int n = dist(engine);

{% endhighlight %}
다음과 같이 쓸 수 있다. 이 코드의 경우, random_device()를 통해 seed를 뽑고, 그 seed를 통해 mt19937()로 숫자를 발생시킨다. random_device()는 확률적인 방법으로 random number를 발생시키는 "True" generator이다. 
  
<br/>

**4. XORSHIFT**  

이런 방법도 있다. 비트 연산과 세개의 seed를 통해 난수를 계속해서 만들어 나간다.
{% highlight C %}
unsigned long xorshift(void){
  unsigned long t;
  x ^= x << 16;
  x ^= x >> 5;
  x ^= x << 1;

  t = x;
  x = y;
  y = z;
  z = t ^ x ^ y;

  return z;
}
{% endhighlight %}

찾아보는 것마다 비트연산을 하는 방법이나, seed가 변하는 방식이나 조금씩 다르다. 구체적인 숫자와 방법은 관련 연구를 하는 사람들의 일이고... 이런 방법을 통해 중복이 적고 빠른 랜덤 숫자가 나온단다.

---

### random에 weight를 주는 방법

이런식의 방법을 생각해볼 수 있다. 똑똑한 사람들...

{% highlight C %}

// Variables
int sum_of_weight = 0;

int num[] = {1,2,3,4,5};
int weight[] = {10, 10, 20, 40, 20};

// Generate Random with Weight
int weightedRandom(){
  
  int rnd = int(double(rand())/RAND_MAX*sum_of_weight);

  for(int i=0; i<RANGE_MAX; i++) {
    if(rnd < weight[i])
      return i;
    rnd -= weight[i];
  }
  
  return -1;
}
{% endhighlight %}

출처 : [stackoverflow](https://stackoverflow.com/questions/1761626/weighted-random-numbers?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

---

참고)  
http://thewiki.ga/w/난수생성  
http://www.devpia.com/Maeul/Contents/Detail.aspx?BoardID=51&MAEULNO=20&no=8632&page=7  
https://stackoverflow.com/questions/1640258/need-a-fast-random-generator-for-c?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa  
https://kplus-biz.github.io/예제/2017/10/27/기본-유사-난수-발생과-메르센-트위스터-구현/  
https://onedrive.live.com/view.aspx?resid=E66E02DC83EFB165!312&cid=e66e02dc83efb165&lor=shortUrl&app=PowerPoint  



