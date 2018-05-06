---
layout: page
title:  "Unicode와 ASCII"
date:   2018-03-27 00:00:00
category: knowledge
tags: unicode ascii
---

한글 작성과 관련된 유니코드 인코딩과 관련해서 뭔지는 알고 써야한다고 생각을 했기 때문에 같이 간단히 조사를 해보았다.


<!-- more -->

<br>

---

### ASCII

`ASCII`는 American Standard Code for Information Interchange의 약자이다. 컴퓨터와 통신 장비를 비롯한 문자를 사용하는 많은 장치에서 사용되며, 대부분의 문자 인코딩이 ASCII에 기반한다. 아스키 코드는 7bit 인코딩으로, 0(0x00)~ 127(0x7F)까지 총 128개의 부호가 사용가능한데, 키보드에 있는 모든 영문자는 아스키 코드로 나타낼수 있다고 봐도 무방하다.

'A' = 0x41, 'a' = 0x61 같이 아스키 코드 표에 대응되는 문자를 뽑아주는 방식이다.

1967년 제정되어 1986년 마지막 개정된 아스키 코드는 현대에 와서 문제점을 드러냈다. 영어권 외에 다른 언어를 표현할 수 없다는 것이 문제.
심지어 7bit로는 영어조차 제대로 표현이 안되었고, 이를 위해서 8bit로 확장한 '확장 아스키 코드'를 사용하게된다. 이 아스키 코드는 OEM, ANSI 두가지 코드체계가 있다.

<br>

---

### EUC-KR, CP949

한국에서는 Unicode가 나오기 전 한글을 표현하기 위해 2바이트를 사용한 인코딩 방식인 EUC-KR을 만들어 사용했다. 1바이트 영역은 아스키 코드와 같다. 이 인코딩에서 표현할 수 없는 한글이 있어 마이크로소프트에서 코드페이지 949를 사용하게 된다. CP949에서는 더 많은 한글을 사용할 수 있다.

<br>

---

### Unicode

따라서, 전 세계의 문자들을 표현하기 위해 유니코드라는 표준 문자 전산 처리 방식이 생겨났다. UTF-8, UTF-16이 유니코드의 인코딩 방식이다. UTF-8방식이 가변바이트를 사용하기 때문에 효율적이고, 세계적으로 많이 쓰이는 방식이다. UTF-8의 경우도 역시 영문/숫자라면 1바이트 영역은 ASCII 코드와 같다.

U+XXXX 와 같이 문자마다 번호를 붙여서 표현한다.


<br>

---

### Window와 Mac의 인코딩 차이로 인한 한글 문제

**1. 한글 깨짐**  
거지같게도, Window와 Mac은 파일을 저장할 때 각각 CP949(EUC-KR의 extension), Utf-8의 형식을 사용한다. 따라서, 윈도우에서 작성된 한글파일을 mac에서 열어보게 되면 스마트한 편집기가 아니라면 한글이 모조리 깨져있는 것을 볼 수 있다. 다음은 [넷 상](http://kkotkkio.tistory.com/86)에서 긁어온 스크립트안데, 현재 폴더에서 cc, cpp확장자를 가진 파일을 euc-kr에서 utf-8로 인코딩해준다.

{% highlight shell linenos %}
#!/bin/bash

find "." -name "*.cc" -o -name "*.cpp" | while read filename
do
  echo $filename
  tempName=${filename}_temp
  mv "$filename" "$tempName"
  iconv -c -f euc-kr -t utf-8 "$tempName" > "$filename"
  rm "$tempName"
done
{% endhighlight %}


**2. 한글 자모 분리**  
Window와 mac의 정규화 방식에 따라 또 한번 한글을 구성하는 방식이 달라진다. "각"을 "각"으로 저장할 것이니ㅑ, "ㄱㅏㄱ"으로 저장할 것이냐를 정해주는 방식이 다르다고 생각하면 된다. [출처 : Pusnow](https://gist.github.com/Pusnow/aa865fa21f9557fa58d691a8b79f8a6d)  

* `NFC (Normalize Form C : Window, Linux)`  
NFC는 모든 음절을 Canonical Decomposition(정준 분해) 후 Canonical Composition(정준 결합) 하는 방식이다. 즉, 각을 각이라는 하나의 문자로 저장하는 방식이다. 이 방식을 사용하면 NFD 방식보다 텍스트의 사이즈는 작아지게 된다. 하지만, 옛 한글 자모의 결합으로 이루어진 한글 음절 코드가 없으므로 이 음절은 Canonical Composition 하지 못하므로 자소가 분리된 체로 저장하게 된다. 이로 인해, 현대 한글과 옛 한글이 다른 방식으로 저장되므로 텍스트를 처리할 때 유의해야 한다.

* `NFD (Normalize Form D : Mac)`  
NFD는 모든 음절을 Canonical Decomposition(정준 분해)하여 한글 자모 코드를 이용하여 저장하는 방식이다. 즉, 각을 ㄱ + ㅏ + ㄱ 로 저장하는 방식이다. 이 방식은 현대 한글과 옛 한글을 동일한 방식으로 저장한다는 장점이 있지만 NFC 방식과 비교하여 텍스트의 크기가 커진다는 문제가 있다.


**3. detect encoding format?**  
인터넷을 좀 둘러봤으나... 새롭게 등장하는 것이 너무도 많아서... 테스트를 좀 해봤다. 윈도우에서 "안녕하세요."가 적힌 txt파일을 그대로 들고와서 mac에서 열어보았다. "안녕하세요."라고 파일 내용이 잘 표시된다. vim에서 내용을 확인하니 "¾È³çÇÏ¼¼¿ä."라고 쓰여있다. mac의 텍스트 편집기 자체에서 파일의 인코딩 형식을 자동으로 파악한다는 것을 알았다.

file을 저장할 때 인코딩 형식을 같이 저장하는 것이 아닐까? 응 아니라고 한다 [(참고)](https://superuser.com/questions/308484/do-text-files-store-their-encoding-method-for-later-decoding]). 그렇지만 때때로 유니코드 내에서는 BOM(byte order mark)를 사용해서 UTF-8인지, UTF-16인지, UTF-32인지를 판단하는 방법을 쓴다.

그렇다면 mac의 텍스트 편집기는 어떻게 인코딩 형식을 알아서 "안녕하세요"를 보여줬는가?? UTF-8는 모든 byte sequence가 valid한 것이 아니기 때문에, UTF-8로 인코딩을 해보고 구멍이 뻥뻥 뚫리면 fail로 판단하고 다른 인코딩 형식일 것이다. 라고 추측하는 방법을 사용한다고 한다. 


<br>

---
참고 :  
https://namu.wiki/w/UTF-8  
http://norux.me/31  
https://gist.github.com/Pusnow/aa865fa21f9557fa58d691a8b79f8a6d  
https://superuser.com/questions/308484/do-text-files-store-their-encoding-method-for-later-decoding]
