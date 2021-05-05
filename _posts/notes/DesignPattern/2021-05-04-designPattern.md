---
layout: post
title:  "DesignPattern"
date:   2021-05-03 21:44:00
categories: DesignPattern
tags: Course
---

### Strategy Pattern

對一個Object, 我們要根據狀況採用不同的演算法來對她做操作時, 我們這時可以用一個algorithm的abstract class encapsulate所有的algorithm. 因此Object只需要hold這個algorithm abstract class, 然後再依據輸入或者狀況set我們的algorithm成特定的演算法(注意, Object是hold algorithm這個data type 但我們是把他的subclass指定給這個變數), 這樣Object只需要call algorithm裡面的method 就會因為polymorphism的關係決定要執行的algorithm的細節.

好處是, 當我們要新增或刪除的演算法時, 只需要動到那個演算法的class, object, algorithm abstract class的部分都不用動到, 能夠做到很好的Extensibility. Loosly Coupled

如圖,context只需要hold algorithm interface,然後call algorithm定義好的function, 就可以依據狀況執行特定的algorithm.

![](/assets/images/notes/DesignPattern/1.png)

### Observer Pattern

#### 簡介

多個Object depend on a main Object, 當一個Object更改資料時, 要能夠讓其他Object上的資料也能夠更新. 所以我們有資料更新就要透過main object來update其他object的資料.

#### 目的

規劃一個1-many的關係, 所以當一個object的狀態改變, 其他所有有關聯的objects也都會被informed

大致上做法為, 首先用一個Abstract class encapsulate所有依賴main object的objects. 然後Abstract class會hold Main Object(所以所有的subclass也都會hold Main Object). 接著Main Object只要跟這個Abstract class互動就不用一一的跟每個object互動. 概念上是Main Object開一個list<abstract class>存所有的objects, 然後每次update就跑迴圈把每個objects內的Main Object data都修改. 

#### 結果

讓開發者之後想要新增新的依附在main objects下的Objects很簡單, 因為loosely coupled, 而且資料也會update

![](/assets/images/notes/DesignPattern/2.png)

實際用法如下圖, ConcreteSubjects就是Main Object, ConcreteObserver就是依賴Main Object的Object. 每次產生新的Observer就會attach到Subject下, 之後只要setState有更新, 所有observer資料都會一起更新

![](/assets/images/notes/DesignPattern/3.png)
