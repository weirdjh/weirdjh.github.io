---
layout: post
title:  "Nand Flash와 SSD"
date:   2018-03-30 00:00:01
category: ssd
tags: nand flash ssd
---

졸업프로젝트를 위한 공부.


<!-- more -->

---

### NAND Flash

![mos]({{site.url}}/asset/ssd/mos.png){:height="300px"}  
(출처 : 한양대학교 Data-Centric System lab)

원래 기존의 ROM은 이름 그대로 "Read Only"로, 비휘발성이 가장 큰 특징이다. PROM이 개발되면서부터 메모리에 1회 쓰기가 가능해졌다. 처음에는 1회만 쓰기가 가능했던 것이 점점 발전하여, 특정한 control을 거쳐 지우기 + 쓰기가 가능한 메모리로 발전되었다. 현재 NOR Flash와 NAND Flash가 대표적이다.

NOR Flash와 NAND Flash는 설계에서부터 태생적인 차이를 가진다. 

>
NOR -> NOT OR gate, 병렬구조  
NAND -> NOT AND gate, 직렬구조
>

이 차이로 인해 `XIP(eXecute In Place)` 가능여부의 차이가 생겼다. XIP는 메모리상에서 program / code를 실행할 수 있는지 여부를 나타낸다. NOR Flash의 경우, 병렬구조로 인해 Byte/Word 단위의 Access(Random Access)가 가능했고 따라서 XIP가 가능하다. 반대로, NAND Flash의 경우, 직렬구조로 인해 Page단위의 Access가 가능하고 XIP가 불가능하다.

NOR Flash는 XIP로 인해 읽기의 속도가 빠른대신, 쓰기/지우기 속도가 느리고, 대용량 데이터 저장에 한계가 있었고, 역시나 반대로
NAND Flash는 읽기 속도가 느린대신, 쓰기/지우기 속도가 빠르고, 대용량 데이터 저장에 용이한 장점이 있다(참고로 두 flash 모두 지우기는 Block단위로 일어난다). 이러한 특성으로 인해 NOR Flash를 SSD로 활용하게 된다.


---

### NAND Flash의 구조

![package]({{site.url}}/asset/ssd/package.png)  

위 그림은 메모리 구조를 간단히 그림으로 그린 것이다. 
* package는 NAND Flash를 겉에서 보았을 때 보이는 검은 칩들이다.
* die는 명령을 실행하거나 상태를 보고하는 등의 작업을 독립적으로 수행할 수 있는 최소의 단위이다.
* plane은 보통 die당 한개 혹은 두개가 들어있으며, 병렬 연산이 일어날 수 있다.
* block은 erase의 최소 단위이다. (4-8MB)
* page는 program(erase)의 최소 단위이다. (4-16K)

---

### NAND Flash의 데이터 저장 방식

낸드 플래시 메모리는 최소 단위인 cell에 몇 bit를 저장할 수 있냐에 따라 `SLC(Single Level Cell)`, `MLC(Multi Level Cell)`, `TLC(Triple Level Cell)`로 나뉜다. 각각 1비트(0, 1), 2비트(00, 01, 10, 11), 3비트(000, 001, 010, 011, 100, 101, 110, 111)을 저장할 수 있다. SLC->MLC->TLC로 가면서 점점 많은 저장공간을 가질 수 있다.

하지만, SLC의 경우 cell에 전자가 있거나(1) 없거나(0)로 값을 구분할 수 있지만, MLC와 TLC는 전자가 얼마나 있는지에 따라 값을 구분해야한다. 즉, MLC나 TLC의 경우 전압의 세기를 각각 4가지, 8가지로 나누어야 한다. 이에따라 복잡성이 증가함에 따라 데이터를 다룰 때 오류가 증가하고, cell의 수명이 짧아지고, 이를 컨트롤할 SW의 복잡도 역시 증가한다.

SLC는 동일한 저장공간이라면 가격이 비싸다. 현재에는 일반적인 용도(하드디스크 대체, 자동차 블랙박스 등)로는 MLC를 사용하고, 스마트폰 보조배터리 혹은 SD카드로는 TLC를 사용한다.

---

### P/E cycle

NAND Flash는 수명을 `Program/Erase(P/E) Cycle`이라고 부른다. 종이에 연필로 쓰고 지우기를 반복하다보면 종이가 너덜너덜해지고 찢어지기에 이른다고 보면 된다. NAND Flash에서는 수명이 다함을 Wear-out이라고 표현한다. SSD에서는 P/E Cycle을 개선하고자 `FTL(Flash Translation Layer)`라는 펌웨어 계층을 두었다. 이 곳에서 OS에서 내려오는 논리주소를 물리주소로 바꾸기도 하고, Garbage Collection, Wear Leveling 등등의 작업을 해주게 된다.

SLC, MLC, TLC에 따라, FTL에서 어떤 정책들을 사용하느냐에 따라 다른 P/E cycle을 갖는데, SLC의 경우 100K cycles 이상을 사용할 수 있고, TLC의 경우 5K 미만의 cycles를 사용할 수 있다고 한다. 이는 일반적인 경우이고, 실제 하드웨어의 상태는 케바케이다. 

참고로, 120GB 용량, 2K의 P/E cycles를 가진 SSD에 하루 10GB씩 쓰기/지우기를 한다면, 120GB * 2K / 10GB = 36,000일 = 약 98년 사용가능... 이런식으로 계산이 된다.

---

### SSD에서의 NAND Flash

![ssd]({{site.url}}/asset/ssd/ssd.png){:height="300px"}  

사용자 요청은 호스트 인터페이스를 통해서 유입이 되고 SATA와 PCIe타입의 인터페이스를 사용한다. SSD 컨트롤러에 장착된 프로세서가 명령을 받아서 플래시 컨트롤러로 전달하게 된다. SSD에는 자체적으로 보드에 내장된 RAM을 가지는데, 일반적으로 메모리 맵핑 정보를 저장하거나 캐시로 사용된다. SSD는 여러개의 Channel을 통해 컨트롤러와 NAND Flash를 연결하고 있다. (출처: [카카오기술블로그](http://tech.kakao.com/2016/07/14/coding-for-ssd-part-2/))

---

### SSD 단점?

이건 그냥 내가 궁금했다. SSD가 비싼 것 외에 HDD와 비교해서 꿇리는 점이 있느냐? 라는 것이다. [나무위키](https://namu.wiki/w/SSD)에 컴덕들이 정리를 해놓았다.

* **전력소모 및 발열** : 미세공정 기술이 발전함 따라 SSD의 엄청난 속도에 의해 발열이 심해지고 있다고 한다.
* **전기적/논리적 오류시 데이터 통째 손실 위험** : SSD는 메모리셀의 화학적 특성을 이용하기 때문에 물리적 충격에 강하지만, 전기적 충격이나 화학적 충격에 약하다. 이로인해 데이터 손실이 생기면 해당 플래시메모리 모듈이 통째로 안녕이다. 기업에서는 RAID기술을 사용하여 백업을 해두지만, 일반 사용자는 복구가 힘들다. FTL에서의 웨어레벨링, TRIM, 인터리빙 등 기술도 복구를 힘들게 한다.
* **불량률** : 불량률이 높다고 한다.


---

참고 :  
http://recipes.egloos.com/4980549  
https://mini_wor1d.blog.me/150071533110
http://www.bloter.net/archives/230738
https://flashdba.com/2014/06/20/understanding-flash-blocks-pages-and-program-erases/
http://aroundck.tistory.com/2055
http://tech.kakao.com/2016/07/14/coding-for-ssd-part-2/