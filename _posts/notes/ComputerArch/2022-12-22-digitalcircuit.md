---
layout: post
title:  "Digital Circuit Design"
date:   2019-05-11 21:44:00
categories: Computer-Architecture
tags: Course
---


為何要做abstraction:

現今電腦架構非常複雜，要一個人從硬體的最底層到軟體的最上層都懂不可能，如果能夠做好各個abstraction能夠做好各個分工，不同人專業於某個領域，最終合起來那就能夠促進電腦的運作與新發展，另一方面是scalability,做好各個分工, 每一層能夠有獨特自己的發展(如ISA就可以有不同的架構，晶片有不同的實作)，而不用擔心影響到其他層，而且能夠用這些設計的原則來製造出更大型的電腦，只要依據interface串接即可，也能做好debug，判斷是哪一層出問題然後修改。

摩爾定律:
每兩年，電晶體(transistor)的大小就會縮小一倍，也就是在相同的面積單位內，可以放入電晶體的數目為原本的兩倍。這有物理上限，但除了塞入的電晶體數目這個問題外，另外就是放入這麼多電晶體是否能夠讓他們都正常運作，例如如何排除運作時電晶體產生的熱，或者電量能源沒辦法讓所有電晶體都在同樣的功率下運作(有的運作功率高，但同時其他可能有些功率低，甚至沒什麼作用)，這兩個會是在塞入更多電晶體時遇到的問題

電晶體基本上就是透過給予電壓的大小來決定是否通電，分為pmos, nmos兩種，兩種特性相反，且消耗的電量也不同，一起作用時叫做cmos,也就是我們常用的電路邏輯的基本單位。透過這些特性我們能夠給予不同的input,來產生不同的output,即and,or,xor,nand,nor等電路邏輯，然後再兜成更大的電路。所以這種電路是透過input不同的電壓，經過電晶體的特性來決定是否會通電來作為boolean的true/false,他不會紀錄上一次的通電狀況(memoryless)，每次通電狀況都只跟當下input電壓有關，稱為combinational logic


Sequential logic(has memory): 用來製作記憶體的元件


### Boolean operation

我們有了基本的and, or等等元件後，就可以來都出複雜的函數/電路邏輯(logic circuit)，
Logic circuit就是由多個電路元件所組合成的一個函數，給定一組input值，電路會為每組特定的input產生一個特定的output值。換句話說，對於input組到output的特定的mapping，我們都能設計電路來達到這些input, output mapping的需求。

##### Lemma & laws

**Demorgan law**<br />

再用於簡化電路邏輯時蠻常用的

```
(a + b + c ... + z)' = a'b'c'...z'
(abc...z) = a'+b'+c'+...z'
```

#### Sum of Product(SOP) && Product of Sum(POS)

由上述知道我們電路可以組成函數來符合需要的intput/output mapping，另一個問題是如何有效的弄成這些電路，即用最簡單的電路或最少的電路元件來組成需要的函數，這樣不但省電，速度也會快很多，是否有SOP能夠達到這個目的，兩個方法就是SOP和POS，首先都是透過truth table透過固定的方法先做出一個能符合預期input/output mapping的電路。接著再做簡化。

##### SOP

先列出Truth table, 把truth table中所有為1的組合，裡面的元件做and(input值如果是1就是原先的0要做complement),然後各個為1的那個equation做or, 這樣就可以得到一個maximum logic circuit(需要很多元件的circuit), 接著再用boolean operation的一些定理把它做簡化，

![](/assets/images/notes/CA/0-1.png)

透過上面的步驟我們可以為每個需求的input/output弄出一個電路邏輯，下一步就是在把它簡化，例如ABC和A'BC就可以合併成BC，AB'C'和ABC'就可以合併成AC', 透過這樣不斷的合併來簡化電路邏輯

```
F = A'BC + AB'C' + AB'C + ABC' + ABC
F = AB' + A'BC + AB
F = A + A'BC
F = A + BC
```

##### POS

就是相反，找出truth table中所有output為0的equation,把equation內的input做or(input值如果是1要做complement, 0 就是原先的)然後各個output 0的equation做and


##### K-map (TBD)

SOP,POS都是透過truth table建立起一個general的電路接著再做簡化，但簡化也需要制式的方法來自動化，以及規則化，方法就是透過K-map來簡化，確保最後簡化出來結果都是最簡單的電路呈現。

#### Combinational Building Block

有些常用到的邏輯，但需要多個and,or來組成的我們常常就先做好，做成一種abstraction,只要知道它的用處，至於實作細節就沒那麼重要

##### Decoder

n input有2^n output, 基本上就是用來trigger某條特定的線路，例如2個input, 那他就會接四個output, 每種input就會在這四個output線路中其中一個是1其他是0, 可以透過input值來trigger特定某條線路，例如可以用於memory address定位，給定memory address(input), 她對應的output線路會是1 其他線路會是0

##### Multiplexer(MUX)

跟decoder反方向，從n個input中選其中一個Output, 透過控制的輸入電路(logn個input), 所以可以透過控制的bit來決定output要是n個input中的哪個，用於CPU的instruction decoder,來決定datapath是否通路

##### Full adder

就是負責一串bit的加法，要考慮carry bit的問題。input兩個數跟一個carry bit, output這個位數和一個carry bit(2個input, 2個output)

##### Programmable Logic Array (PLA)

就是Sum or Product的狀況， n個input n個output, 中間會向Sum of product產生個個n個input的組合，接著有一個區段是使用者能自己決定電路，要怎麼接那個2^n個可能組合來接到三個output，所以使用者可以透過在區段中自己插入and,or gate來自己決定input值和output值的mapping. 基本上這就是一個programmable circuit, fpga就是運用類似的概念來達到programmable circuit。對於n個input, PLA是可以產生所有這n個input可能產生的output的mapping

#### Tristate

基本上就是透過一個控制bit來決定輸出要是甚麼，例如當input bit是1, 然後控制器也是on，output才會是1, 如果控制器是off, 不管如何都是output 0. 控制器是signal 通常不是input, 電路決定。

### Sequential Logic

一個combinational logic加上storage element, 因此有記憶能力，能記住上一次的output

#### storage element

RS latch(Reset-Set) and flip-flops: 超快，可以平行存取，但很貴(存一個bit要數十個transistor), d-latch可以用來製作register, 一個d-latch可以存一個bit, 所以可以用多個d-latch, 然後data接到各個latch,跟一個write-enable bit接到所有的d-latch

sram: 速度在中間，一次存取一個data word, 較貴(存一個bit要6個以上transistors)

dram: 較慢但便宜，一次只能存取data word，一個bit的存取只需要一個transistor + capacitor

其他storage: flash memory, hard disk, tape,  這些更慢，但更便宜(不用transistor)，而且是non-volatile(沒通電時資料還是會在)


##### clock

一個機制讓sequential logic從一個state轉到另一個state(例如bit從0變成1), 它可以讓多個sequential circuit同步，多個circuit都根據同個clock轉換state。


### Verilog

因為硬體電路設計現在非常複雜，需要大量的電路跟電晶體，所以需要一個方法來讓整個電路設計更簡單，更能讓人討論這些design, 以及模擬電路結果，以及自動化某些步驟(例如常用的component自動化), 這就是Hardware Description Language(HDL)

#### Module

設計一個複雜電路一個方法就是利用top-down的形式，先想想問題可以拆解成哪些大的modules,這些大的modules又要怎麼拆成小的Submodules, 逐漸的divide到可以時做的地步再慢慢merge回去

Module要有名字，input/output ports, 各個port的名字,input/output可以是single bit, 也可以是multi-bit(array的形式)