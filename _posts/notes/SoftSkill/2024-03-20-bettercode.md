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

- Simplicity:
    - 一個function, method只做一件事
- Open-closed Principle: open for extension , close to modify
    - 很容易擴充(即加新功能不用修改舊有的code)，但是本身內部核心是不太需要因為新功能修改的
- Liskov Substitution
- Interface Segregation
- Dependency Injection

#### Boy Scout Rule

leave the code better than you found it.

#### Reference

[twitter](https://twitter.com/mwaseemzakir/status/1770159698404364756)<br />
Extreme Programming Explained
