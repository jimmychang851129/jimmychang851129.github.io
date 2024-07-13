---
layout: post
title:  "Forward Declaration"
date:   2024-07-01 21:44:00
categories: Algorithm
tags: Course
---

### 前言

在進行C++大型專案時，常常會遇到的compile time過久的問題。而追根究底，往往就是跟cpp檔include header file有關, 而解法也往往是透過forward declaration來在header file裡面include其他structure /class. 以下介紹header file以及forward declaration的發展及用途。

### 背景

#### header file的目的

**背景**<br />
主要目的就是modularize,  在沒有header檔之前，寫程式的人最初寫法就是把整個程式碼都寫在一個c檔，但這會導致程式碼很長不好維護，也無法合作開發。

**檔案拆成多個c檔**<br />
接著就有人想到可以把檔案拆成多個.c檔，編譯時一起編譯, 交給compiler去找某個symbol究竟在哪個.c / .o檔在中在把他link起來。 但這件事仍會沒有辦法有很好的模組化，而且會有幾個問題

例如我希望某個macro在特定c檔都有，或者某些structure/class只在某些c檔有，這件事情在沒有header的狀況下就是要一個檔案一個檔案自己進行宣告，並且當宣告的structure/ macro等等的東西一多，就容易在處理時出錯，例如複製沒複製好，或者要做修改，要進去每個宣告的檔案修改，很容易漏掉

**header file的出現**<br />

header file (.h)就是把宣告的structure/ macro都寫在裡面，這樣需要的人可以include這些宣告，要修改介面或者header內容時，直接進去header file裡面修改就好，就不用像以前只有c檔時要進去每個c檔裡面找裡面看

**In summary**<br />

header檔的目的就是為了讓某些宣告(Declaration) 能夠統整再一起，一起被共用，想修改時又有一個地方能夠修改就apply到所有地方。注意的點是header file在compile時是直接preprocess時被放到.c檔裡，因此不建議在header file裡面有太多東西或者實作，一來有時做會違反ODR (One-Definition Rule)，二來這樣preprocess會讓每個object檔都變超大。增加preprocess的功, linker的功。

### 關於多個object檔連在一起的問題

上述的作法很美好，但將cpp檔案分開成多個檔案開發實務上會給編譯器造成很大的麻煩。

上述分成多個檔案的編譯流程會變成:<br />
1. 將各個c/cpp檔案獨自編譯成object file
2. linker在把object file合併成一個.a或者執行檔

第二步的其中一個步驟為linker會去把object file裡面還沒解開的symbol去各個object file找他對應的definition，因為單一的cpp檔會使用到其他header/cpp檔定義的symbol (如structure, function, class等等)。 當看到一個目前cpp檔案裡沒有的symbol，這會導致問題如下:

1. 編譯器不知道這個symbol是真的存在還是user打錯字
2. cpp允許function override, 很多function可以有相同名字，compiler要判斷是哪個override function極為困難，甚至還要判斷user傳錯參數這件事

為了解決上述compiler難題，c++ compiler會設定單一個compile unit(cpp檔) 每個symbol都要被declared, 一來能夠確保使用者不是手殘打錯字，二來確定使用者想call的override function的型別，這樣compiler可以根據user提供的declaration去其他object file找到對應的symbol definition. 

這種因為symbol定義(definition)在其他檔案，而把symbol先declare在cpp的方式，稱為forward declaration.

**注意:** 如上所述，forward declaration只是宣告user預期檔案中使用到的symbol的長相(function signature, structure/class name等等), 並不包含該symbol的細節。


### Forward Declaration 和 include header file

因此我們知道今天寫好一個cpp如果用到的function / class / structure定義在其他檔案，我們有兩種處理方式

1. 把structure, class, function的declaration寫在header, 然後cpp檔include header
2. 直接在cpp檔宣告這幾個structure

以下為範例，分別說出何時使用forward declartion以及header file

1. 當cpp使用到class, structure的member, method時，這時候就需要include header file, 原因是這時候會需要知道此data type的細節(data有哪些member),  forward declaration並不含有這些資訊，所以會報錯。
2. 當header file過大時，而我們cpp檔只要使用header中的某幾個structure/ class, 可以直接使用forward declaration避免引入過多不必要的function


總結而言，最理想狀況就是每個header file都超小，cpp用到什麼就include該header file, 不用特別forward declaration.

但是一但header檔dependency太大，而cpp檔只用到header中的幾個structure/class, 可以使用forward declaration來減少compile time, 避免include header造成不必要的parsing。

但是當cpp使用到了structure/class的實體member/method, 這時就只能include header, 因為這樣compiler才能static檢查是否呼叫method/member正確，forward declaration並沒有包含data type的細節資訊。

a.cpp
```
void ab::print() {

}
```

a.h
```
class ab{
    int a;
    int b;
    void print();
}
```

main.cpp
```
#include "a.h"  // 1. 透過這種方式把declare structure

class ab;   // 2. 透過forward declaration, 這種方式不用include a.h

int main(){
    ab test;
    test.print();   
    // 透過forward declaration方式 呼叫class method會有compile error: imcomplete definition error
}

```

#### 結論

- 如果header很小，可以直接include header
- 如果程式編譯時間過長，或object file太大，可以改用forward declaration來加速編譯時間，跟減少dependency
- 如果需要full definition時 (e.g.使用到structure / class member 或method)  就只能用include header 不能forward declaration
- 如果只使用pointer, reference,  可以用forward declaration

Reference:<br />
- [When should I use forward declaration](https://stackoverflow.com/questions/553682/when-can-i-use-a-forward-declaration)
- [What are forward declaration](https://stackoverflow.com/questions/4757565/what-are-forward-declarations-in-c)