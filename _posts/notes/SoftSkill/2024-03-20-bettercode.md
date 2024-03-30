---
layout: post
title:  "如何讓code變得更好"
date:   2024-02-27 21:44:00
categories: SoftSkill
tags: Course
---

此篇文章是根據以下圖得到的心得

![](/assets/images/notes/SoftSkill/4.jpeg)

一開始都是先求有再求好, 而中間求好的步驟可以分成以下階段，一步一步來，畢竟處理技術債要一步到位太難，但如果分階段來做，就會感覺整個修改是可行的，然後更能評估目前的進度跟具體該做的事。

#### YAGNI

You Aren't Gonna Need It.  對於功能與需求，除非真的不得已，不然不要再加新的API,  API越少越好管理，極簡主義也讓整個複雜度下降。 因此有相似的功能, 需求，盡量以同一個API為主，不要因此開多個API.

#### Principle of least surprise

最小驚訝原則，開出的API盡量符合user期望的行為，不讓user感受到意外或不預期的輸出。驚訝係數很高的API就有必要重新設計。

#### Keep It Simple Stupid

就如字面上的意思，盡量讓API邏輯越簡單越好

#### Don't Repeat Yourself

對於重複的邏輯，盡量共code, 不共code就容易產生bug, 例如一個地方修到，另一個地方沒修到

#### clean code

原始碼本身很清楚，而且相關的資訊、概念、規則及程序是直覺上容易理解的，也就是在合理的時間，不用花非常多的心力即可以理解的程度

#### SOLID

**Simple Responsibility Principle**<br />
一個function, method只做一件事<br />
更精準來說: A module should have one, and only one, reason to change

如果一個class, function有太多功能，常常導致coupling而有side effect或者不預期的flow, 結果, 也比較難修改

**Open-closed Principle**<br />
簡單而言就是加一層抽象，而根據這個抽象再有不同的實作細節，抽象層定好規範後，不論實作為何都要遵守這個抽象的規範(input,output)等等。但scalable意思是可以透過這個抽象層在建立新的物件來處理各種狀況，達到scalable。

Reference: [深入淺出開放封閉原則 Open-Closed Principle](https://www.jyt0532.com/2020/03/24/dip/)

**Liskov Substitution**
**Interface Segregation**

**Dependency Injection**<br />
1. high-level的物件不應該依賴low-level物件的實作細節, 兩者都要depend抽象層
2. Abstractions should not depend on details. Details should depend on abstractions.

關於點1, 代表可能高層物件使用低層物件，會有的問題是當低層物件要scalable, 高層物件也要跟著一起動，**因為他depend, 直接使用低層物件的實體** ，如果要讓他低耦合，高層物件應該是depend一個abstract layer, 而低層物件根據這個abstraction實作，這樣比較容易根據狀況替換成另一個低層物件。

#### Boy Scout Rule

leave the code better than you found it.

#### Reference

[twitter](https://twitter.com/mwaseemzakir/status/1770159698404364756)<br />
Extreme Programming Explained


#### Stick to Boring Architecture as long as Possible

Reference:<br />
[Stick to boring architecture for as long as possible.](https://addyo.substack.com/p/stick-to-boring-architecture-for)

此文擷取至Addy Osnami的文章

當我們在進行專案或者做某些改善時，總會想著套用最新最潮的東西來解決問題，可能是新的library, 新的tool, 新的architecture, 但要釐清這些東西究竟是**適合**這個問題還是只是潮而已。

但作者認為其實很多時候那些歷久不衰的old, boring architecture才是真的經過千錘百鍊，許多改良以及測試過後的產物，比新的東西更適合被套用。

**From Creativity to Value**<br />
作者提出軟體的fix, enhance，所有的修改的核心價值是**能夠給user帶來價值**，因此不管是各種fix的優先程度以及創新想法，最終值不值得做都要回歸能帶給user多少價值，還是只是自己開心而已。 

因此題外話是對proposal, 要有一個metric來模擬、衡量一個solution或者idea它能夠帶給user多少價值，到底能解決什麼問題，能夠帶來多大的效益，即便他可能帶來一些instability和加深learning curve跟code complexity.<br />
效益可以是讓user更願意使用，可能是幫助user更快完成task，可能是減少user踩雷遇到bug的機會等等。

作者建議：Begin your project with a solid foundation based on well-understood technology.

一開始fix,或者開始做project時，可以先使用簡單，基礎的設計，再根據需求套用新的東西。因為在做的過程中就會開始知道具體會遇到哪些challenge，這時候就會有更好的理解到底該使用什麼tools, architecture來處理問題，或者針對這些特定的問題點來使用新的技術或tools處理，但整個架構本質來試boring but stable的architecture.


### Code review guideline

**function**<br />
1. 定義清楚function的input/output
 - 函式參數的type, range應該要在一定範圍, 超過應該要assert
 - return值應該盡量避免magic number, -1, 0 之類的，可以使用std::optional回傳以示失敗
 - return值可以的話ㄧ樣盡量定義清楚output值域，不合理的值assert
2. 一個function盡量維持單一一個功能，不應該一個function做太多事，做太多事就切成另一個function
3. 盡量避免使用global, static variable, 盡量讓function每次呼叫沒有side-effect

**變數命名**
1. 盡量有意義的命名, 避免像id, tmp這種命名
2. 一個變數一個功能，也不應該橫跨太長的code
3. 能宣告const盡量宣告const
4. use smart pointer instead of pointer if possible
5. 使用變數前，先assert確保他的值是如預期(例如non null, 非0等等)