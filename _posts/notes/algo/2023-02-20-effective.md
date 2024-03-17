---
layout: post
title:  "Effective C++"
date:   2022-02-20 21:44:00
categories: Algorithm
tags: Course
---

### Decltype

decltyp會保留所有對變數的修飾,例如constant, reference都會保留，但auto不會

```
const int &a;
decltype(a) -> const int&
```

### Item 17()和{}兩種初始化方法的不同

```
Widget w1;
Widget w2 = w1; //實際上是呼叫了w1.ctor(), w1的constructor
```

{}初始化叫做uniform initialization, 就是基本上各種東西的init, 用這個一定行得通, 有些不能用=(atomic operation), 有些不能用括號(non-static class member)
{}的方式允許vector初始化時客製化要放進去的元素, 是其他初始化方式做不到的

```
vector<int> s{1,2,4};
```

{}最大的問題在於如果constructor有initialize_list這個data type時會有mistmatch的問題, 就是當class constructor有parameter是initialize_list, 那他的priority會超高，導致其他看起來更合的constructor沒有被呼叫到而是呼叫到有initialize_list參數的constructor

```
Class widget{
    widget(int, bool){....}
    widget(initialized_list<double>);
}

widget w1{5,true} -> call second constructor, initialized list有最高優先度

```

#### Item 18 nullptr

prefer nullptr than 0, NULL

因為0, NULL他們可以有多重意思, 0可以是整數的0,也可以代表null，那位導致ambiguity, 舉例如下<br />
這個例子並不能知道t究竟是整數還是NULL, 是boolean的null的話t+=1會出錯

```
auto t = check();
if(t == 0){
    t += 1;
}

因此改成
if(t == nullptr)
就可以避免各種問題
```

又如以下function overload,可能也會產生ambiguity, 因此在overloading時要盡量避免同時overload integer跟pointer, 不然在0,NULL的case會不知道call哪個function

```
f(int);
f(void *);
f(bool)

f(0)是呼叫f(int),

如果是f(nullptr)就毫無疑問的是呼叫f(void *)
```

最大的問題在於template使用, template deduction對於0,NULL可能會deduce出錯誤型態的function

```
template<typename T>
decltype(auto) f(T){...}
```

f(0) 實際試試deduce f(int), 但f(0)可能代表的意義是我要deduce f(void *)然後0代表null pointer<br />
同理f(NULL)也會deduce出f(int),導致跟預期不一樣的function paramter

### Smart Pointer

C++新的功能讓指標更使用者友善，處理很多底層的記憶體處理避免程式開發時的錯誤。讓指標的memory allocation, free, 能夠得到更好的處理，避免double free(一條code path free了一個物件兩次)，或者memory leak(忘記free物件), 或dangling pointer(避免pointer free完還指向該記憶體位置)

也就是呼叫smart pointer, 他基本上就是把pointer變成一個class, 一離開scope class會自動刪掉，那smart pointer的class 刪掉時就會自動呼叫該object的delete, 或者可以有一個custom deleter, 執行特定的delete或者做其他事，如logging。

Smart pointer也能確保該object不會被double free, 每條code path只會被free一次，而且遇到exception, early function return, 或break等等影響control flow的狀況仍會被刪除, 少數情況如exit, abort被呼叫, 跳出thread的primary function, 這些情況不會呼叫object的destructor。

#### New & Delete

C++提供了新的memory allocation的function, 跟C的malloc, free相比，除了allocate memory外，new和delete還會呼叫structure或class的constructor來做初始化，所以會比malloc, free來得方便，不用自己做data type casting跟initialization

new本身包含三個步驟:<br />
1. malloc一塊記憶體空間
2. 呼叫物件的constructor來初始化物件
3. 把這個空間assign給等號左邊的變數

這會出現的問題是可能會因為優化導致reordering或者context switch的關係而做到一半被switch(這些operation不是atmoic),所以可能會發生switch時，已經有allocate空間且指標已經指向他，但還沒初始化，或者已經初始化完，但還沒把他assign給pointer

#### unique_ptr

unique_ptr只能move, 轉移物件的owner, 原本owner變成指向null, 不能copy。

一個unique_ptr常用的方式是把它當factory pattern的回傳型態

```
template<typename... ts>
unique_ptr<Investment> createInvestment(ts&& ..params){
    unique_ptr<Investment,delInvmt> pInv(nullptr,delInvmt);  // create nullptr
    pInv.reset(new Stock(....)) //create object and point to it
}
```

這個寫法直得注意的是,  他是指向一個Investment的datatype, 所以delete時也是呼叫Investment的destructor, 因此如果要多型, create的是Investment的derived class, 那就會是derived class呼叫到base class的destructor,可能有memory leak, 所以這種有繼承多型的狀況, 要把destructor宣告成virtual function.

unique_ptr<datatype, deleter>, 一個unique_ptr的data type會根據datatype, deleter決定他的data type, 即使data type一樣，但custom deleter不同仍算事不同物件, custom deleter可以讓使用者在物件被free之前做一些其他事，例如做logging紀錄

unique_ptr理論上是一個raw pointer大小，指向物件，除非有custom deleter, 如果是一個funcion pointer指向deleter, 那unique_ptr的大小變成兩個raw pointer，但如果是傳入function object, 那就會跟function object的實作有關，如果function object裡有很多變數，state, 那unique_ptr的大小就會變很大，所以偏好用function pointer或者stateless的lambda function(with no capture)減少deleter可能佔據的空間

unique_ptr的data type有分個別的T跟陣列的T[], 分別指到單一物件或者一個陣列，unique_ptr<T>, unique_ptr<T[]>，這樣可以避免ambiguity, 可以知道這個指標指導的東西一定是一個物件還是陣列，陣列就沒有*的dereference,單一元素就沒有[]的dereference方法

此外unique_ptr可以自動轉成shared_ptr, 但反向不行，所以factor pattern的函示雖然回傳unique_ptr, 但可以用shared_ptr去接，那他就變成shared_ptr的形式, unique_ptr不接受raw pointer轉換成unique_ptr

#### shared_pointer

一個shared_pointer的object含有兩個指標, 一個指向object, 一個指向shared_pointer的dynamic allocated的control block, 紀錄reference count, function object, allocator... . reference count紀錄有幾個shared_ptr指向該物件, 當ref_count變成0時就會把物件刪掉。

ref_count的修改要是atomic operation, 例如新的shared_ptr產生, 舊的delete, 甚至assign operation(=) (move運算不會影響ref_count), 這些運算和ref_count加減都要是atomic, 因為在multi thread環境下可能會有race condition, 例如一個要刪掉, 一個要增加ref_count, 那就會產生 race condtion或者不預期結果, 所以operation跟ref_count加減要atomic


跟unique_ptr不同shared_ptr的custom deleter不是data type的一部分

```
unique_ptr<Widget,decltype(logdel)> upw(new Widget,logdel);
shared_ptr(Widget) spw(new Widget,logdel)
```

一般來說, create shared_ptr時point到一個物件, 我們沒辦法知道他的control block在哪，所以有以下的規則:<br />
- make_shared一定都會產生一個新的control block (make_shared沒辦法宣告custom deleter)
- 從raw pointer直接轉成shared_ptr也會產生新的control block
    - 如下所述，但這不是一個好作法，比較好作法應該是用new的方式

```
auto ptr = new Widget
shared_ptr<Widget> spw1 = shared_ptr(ptr,logdel)    // create control block
shared_ptr<Widget> spw2 = shared_ptr(ptr,logdel)    // create control block

shared_ptr<Widget> ans = shared_ptr(new Widget(),logdel)

shared_ptr<Wdiget> same = shared_ptr(ans)   // same control block as ans
```


而raw pointer這種init方式產生一個新的control block, shared_ptr會造成一些問題, 如下:

emplace_back會直接push該物件而不是construct一個新物件, 但是這種呼叫方式卻會讓物件construct一個新的物件跟control block並指向他, 而失去emplace的目的

```
vector<shared_ptr<Widget>> vec;
class XXX{
    vec.emplace_back(this) // 透過this建立一個shared_ptr, 
}
```

要避免this建立一個shared_ptr

