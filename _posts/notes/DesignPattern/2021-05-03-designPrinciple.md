---
layout: post
title:  "DesignPrinciple"
date:   2021-05-03 21:44:00
categories: DesignPattern
tags: Course
---

### Why Design Pattern

我們把常見的遇到的問題跟對應的解法弄成一個既定的pattern. 一但遇到一樣的問題時可以套用一樣的設計方式來解決他<br />
另外是我們根據這些principle, 之後的程式會更flexible也比較好擴充新的功能或object, 所以在設計時就應該要先確定那些component之後是可能會擴充或改變的

#### 描述一個Design Pattern

1. Pattern Name
2. Problem: 說明在哪種情況下要用這種design pattern
3. Solution: 具體而言要怎麼設計
4. Result: 設計完的結果, 例如增加
    - flexibility: 系統能夠handle不同的狀況
    - extensibility: 未來想要擴充, 如增加新功能時會比較簡單, 不用東修改一點code,西修改一點code, 只要加一個新的class extends abstract class就好之類的
    - portability: 在不同環境下仍能順利執行

#### Category

##### 依據Scope來分

- Class Patterns: class之間的關係(例如繼承之類的), 所以關係的確立是在compile time時確立
- Object Patterns: object間的關係,例如composition, 所以是run-time時確立的關係

##### 依據Purpose來分

- Creational: Design Pattern跟create新object有關
- Structural: Compose class或者Object
- Behavioral: 主要focus在class間怎麼互動跟分配工作

#### 規劃Design Pattern

1. 先依據requirements做出Initial Deisgn(人體Compiler, 把requirements轉成簡單的Class Diagram)
2. 先確立那些component之後是會擴充或改變的
3. 依據Design Principle把它優化

![](/assets/images/notes/DesignPattern/6.png)

### Principles

#### High Cohesion, Loosly coupled

每個class只做簡單的task, 不會同時做多個tasks, 然後class間是low dependency的, 也就是當我想要擴充其他class或者修改class的東西時, 不太會動到其他class的code, 也不用了解我需要使用到的object的細節, 所以是loosely coupled.

#### Inherit the most important and delegate the rest

一個class如果同時要繼承兩個abstract class在java是不允許的, 而且這樣會變得容易有bug或者程式複雜(例如繼承多個class然後兩個parent class可能會有些地方互相衝突), 而且會讓程式的行為變得複雜(代表他不只是focus在單一的task上). 所以我們只inherit最重要那個class 剩下比較不重要的class但我們需要他的feature的話, 就使用delegation.

#### Encapsulate what varies

如果多個method其實是做一樣的事情只是因為條件不同而有些微implementation上的差異, 就可以把這些method encapsulate出去成一個一個的class, 然後這些class都有這個method只是implement上有點差異, 這樣主要的class就可以單純呼叫這個method 在依據呼叫的class做不同的事.

例如做pizza不同的pizza implement方式不同, 我們就把pizza弄成一個abstract class然後不同style的pizza繼承這個abstract class且都有一個method叫做createPizza, 這樣user就可以直接set pizza style然後呼叫createPizza就可以得到他想要的特定的Implement方式, 而不是一連串的if else寫在User code裡

#### Favor Composition over Inheritance

Inheritance一大特點在reuse-code, 但她限制也比較多,也比較highly dependent, 例如parent class有修改, 下面subclass通常也要跟著動, 所以class間的關係比較不flexible. 因此比較好的作法是通常用Abstract class(Partially Implement).

Composition特點在於我們只hold這個Object且呼叫他的function, 至於它裡面實做細節或者他是這個class下面哪個subclass其實我們不care.

#### Program to interface not implemenatation

當我們在做composition或delegation時, parameter最好放的是最上層的interface或者abstract class而不是放它下面的subclass或者implementation, 因為parameter放abstract class或interface有比較高的flexibility, 下面的subclass都可以當成參數傳進來

#### Open-Close Principle

Class能夠簡單的extends然後不會影響或修改到舊有的code, 通常代表就是要用一個abstract class, 要extends時就是寫一個新的class繼承他

#### Dependency Inversion Principle

讓high-level跟low-level components都作用在Abstract class上

舉例而言, 當一個highlevel module同時會使用到多個LowerModule時, 或者這些LowerModule其實做的事情一樣只是因為condition不同時implementation細節稍微不同, 此時就可以把這些LowerModule弄一個Abstract class, 然後所有相關的LowerModule都繼承這個class, high-level module則是呼叫這個class就行. 這樣所有class都是透過這個abstract class作互動, high-level class也可以透過set class在呼叫abstract class的method來執行特定的事情

![](/assets/images/notes/DesignPattern/4.png)

![](/assets/images/notes/DesignPattern/5.png)


這樣好處是當我們lowerModule要擴充(例如新增一個新的LowerModule時), 我們不會去動到High-levelModule的code)

#### Hollywood Principle

high-level component可以呼叫low-level component, 但反過來不行

#### Create Object indirectly (Dependency Injection)

盡量不要在一個class的Method裡面new另一個Object,而是透過setter或constructor方式先把它存在class的private variable之類的, 透過另一個injector class/object來達到這件事. 一來能夠讓class focus在他真的需要完成的task上而不是要讓他負責更多的task(handle其他object的creation). 一來class的creation都交給原本那個class做的話,會很難知道是create class出錯還是我們現在這個Class的邏輯出錯(不容易debug), 而且也沒達到loosely coupled, 一但修改了created class的內容, create他的class可能也因此要動到)

#### Extend functionality by sub-classing and composing its instances with existing one


