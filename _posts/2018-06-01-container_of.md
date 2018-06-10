---
layout: page
title:  "container_of"
date:   2018-06-01 00:00:00
category: C
tags: container_of linux kernel
---

linux kernel을 읽던 중 매우 우아한 코드를 접했다. C로 객체 비스무리한 개념을 만들어 쓴다. 와우. Kernel 개발자들이야 말로 C에 통달한 분들이 아닌가 싶은 생각이 들었다.

---

## offsetof

{% highlight C %}
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
{% endhighlight %}


**1. (TYPE *)0**  

>0을 `struct TYPE`으로 casting한다. 이는 컴파일러가 data segment영역의 시작 부분에, `struct TYPE`을 하나 가지고 있다고 생각하게 한다.

**2. ((size_t) &((TYPE *)0)->MEMBER)**  

>`struct TYPE`의 MEMBER의 주소를 size_t의 형으로 반환한다. `struct TYPE`의 처음 주소가 0 이므로, 결국 MEMBER가 가지는 offset이 된다.

data segment영역의 메모리에 함부로 접근하는 것은 위험하지 않느냐? 라면 이 코드는 그렇지 않다고 한다. 왜냐하면, 실제로 메모리에 접근한적이 없기 때문이다. 모든 계산들은 stack과 register를 통해 이루어질 뿐이다.


(출처 :[stackoverflow](https://stackoverflow.com/questions/18554721/how-to-understand-size-t-type-0-member?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
))

---

## container_of

{% highlight C %}
/**
 * container_of - cast a member of a structure out to the containing structure
 * @ptr:	the pointer to the member.
 * @type:	the type of the container struct this is embedded in.
 * @member:	the name of the member within the struct.
 *
 */

#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})
{% endhighlight %}


**1. const typeof( ((type *)0)->member ) *__mptr = (ptr);**

>`typeof`를 아무생각 없이 사용해왔지만, 이상하다고 느꼈다. 어떻게 `typeof`를 매크로 안에 쓸 수 있는 거지? 정확히 말하면 표준 C가 가지고 있는 문법은 아니고, [GNU extension](https://gcc.gnu.org/onlinedocs/gcc/Typeof.html)이라고 한다. gcc 컴파일러에서만 컴파일 가능하다.

비슷한 모양의 sizeof가 있는데 요놈은 참고로 unary operator이다.


**2. (type *)( (char *)__mptr - offsetof(type,member) );}**

>왜 굳이 (char *)로 casting을 해줬을까? 이는 char형의 크기가 1byte이기 때문이다. 

{% highlight C %}
int a[20] = {0};

int *p = &a[3];
int *q = &a[13];
ptrdiff_t diff1 = q - p; // This is 10

char *x = (char*)p;
char *y = (char*)q;
ptrdiff_t diff2 = y - x; // This is 10 times sizeof(int)
{% endhighlight %}

diff의 경우 int의 크기를 기준으로 차이를 저장하기 때문에 10이 되지만, diff2는 char의 크기를 기준으로 하기 때문에 10*sizeof(int)를 곱한만큼이 저장된다. 따라서 메모리 사이의 크기를 정확히 알기 위해서는 (char *)로 casting이 필요하다.

(출처 :[stackoverflow](https://stackoverflow.com/questions/39854610/casting-int-pointer-to-char-pointer))

 ---

## Example

{% highlight C %}
#include <stdio.h>
#include <stdlib.h>

#define offsetof(TYPE, MEMBER) ((size_t) & ((TYPE *)0)->MEMBER)

#define container_of(ptr, type, member) ({     \
      const typeof( ((type *)0)->member ) *__mptr = (ptr);  \
        (type *)( (char *)__mptr - offsetof(type,member) );})

struct FRUIT{
  int price;
  int weight;
}*apple; 

void other_function(int *via);

int main(){

  apple = (struct FRUIT*)malloc(sizeof(struct FRUIT*));
  apple->price = 100;

  other_function(&(apple->price));

  return 0;
}

void other_function(int *via){
  struct FRUIT *new_apple = container_of(via, struct FRUIT, price);
  printf("%d\n", new_apple->price);
}

{% endhighlight %}

출력결과는... 100이 된다. 아주 잘된다. 놀랍다.

---

