---
layout: page
title:  "rounding up to power of 2"
date:   2018-05-28 00:00:00
category: algorithm
tags: kernel
---

linux kernel의 코드를 보던 중 멋진 함수를 발견했다. 새삼 놀랄 일은 아니지만.. 코드를 소개하면 이렇다.

{% highlight C %}
static __inline__ int get_count_order(unsigned int count)
{
	int order;

	order = fls(count) - 1;
	if (count & (count - 1))
		order++;
	return order;
}
{% endhighlight %}


**fls**는 어떤 숫자의 msb를 리턴하는 함수이다. 내가 궁금했던 건, 도대체 **if (count & count - 1)**의 역할이 뭐냐는 것이었는데... 숫자를 몇개 써내려가다보니 멋진 것을 발견할 수 있었다.

10000 : 16  
**01111 : 15**  
**01110 : 14**  
**01101 : 13**  
...  
**01001 : 9**  
01000 : 8  
**00111 : 7**  
...


2의 제곱수가 아니면 order에 1을 더해주는 것을 알 수 있다.  


{% highlight C %}
/*
 * pblk_rb_calculate_size -- calculate the size of the write buffer
 */
unsigned int pblk_rb_calculate_size(unsigned int nr_entries)
{
	/* Alloc a write buffer that can at least fit 128 entries */
	return (1 << max(get_count_order(nr_entries), 7));
}

// linux/driver/lightnvm/pblk-rb.c 
{% endhighlight %}

위 코드를 보면 어떤 쓰임새를 갖는 지 알 수 있다. 엔트리의 개수를 받아와서 메모리에 담으려면 필요한 메모리 단위 수가 몇개인지를 출력해준다.