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

### Decorator Pattern

#### 簡介

用於當每個Object有略微不同的功能或特性時(例如某Object有A,另一個Object只有B, 第三個Object有A,B,C). 這種狀況就可以用Decorator

#### 目的

可以動態Attach新的功能或特性(上述A,B,C)給原有的Object, 不用code寫死. 做法是把這些Components(A,B,C) abstract起來後繼承最大項的那個Interface, 所以Component A,B,C是一個abstract class,而這個abstract class是implement最上層那個Interface, 相反的, 其他Object(有不同的A,B,C feature)的就直接implement最上層那個Interface.

這些A,B,C就是decorator, 也是一種Object, 動態產生然後可以依據需求Attach或Detach到Main Object

這樣Call decorator的Concrete object時,是遞迴的call, 也就是Concrete Object(A,B,C)的method的implementation其實是call super.method(), 呼叫上層Interface的method.

![](/assets/images/notes/DesignPattern/7.png)

如圖, 例如visual component我們可能會有不同的view, 如textview, imageview, 而每種view都會有不同的components(例如有的有border,有的有scrollbar, 或者有的都有), 因此我們要能夠動態run-time的attach這些component給textview,或者能夠動態新增components, 這時就可以採用這個方法, 而這些scrollbar,border就是decorator來裝飾view object

#### 結果

view object能夠動態的attach,detach不同object,也可以隨時新增不同的component然後attach到view object, 只要他是implement decorator的interface即可

### Factory Pattern

#### 簡介

用於當我需要依據狀況create不同的objects(這些object都會有common behavior,只是會有些部分不同), 如果我想要dynamic狀況create這些object而不是在code裡面寫死 如下

```
if(a == "dog") this.ret = new Dog();
if(a == "cat") this.ret = new Cat();
...
```

那就可以用Factory來做

![](/assets/images/notes/DesignPattern/8.png)

#### 目的

先用一個Class把這些相似但略為不同的Class Abstract起來<br />
利用一個Factory(Object creator) 把object的creation外包給這個object來做, 然後利用HashMap形式來決定要call哪個object的constructor

所以, 每個animal會有一個factory class, catfactory,dogfactory,他們裡面會有一個create method專門return該動物的object. 然後這些factory因為有common behavior,所以absract起來,全部繼承animalfactory這個abstract class

因此main function要create animal時,透過hashmap傳入的string來決定要call哪個factory class的create method就可以產生特定的animal了

#### 結果

之後就可以利用hashmap參數來決定要create哪個object, 要新增object時也不用特地在加if/else的code或者插入新的constructor, 只要新的class繼承abstract class 然後弄好constructor, 把這組資料insert到HashMap裡就完成. 不用動到Main Code


### Bridge Pattern

#### 簡介

用於當一個abstract class可以有多個不同的implementation方式(即可能有不同的method, variable)時, 也就是說會需要在run-time改變自己現在的這個object, 可以呼叫的method.

一個例子是我定義了兩種不同的windows(XWindow,PWindow)他們分別有不同的功能, 然後他們可以用來實作iconWindow或者AWindow兩種window, 即iconWindow,AWindow這兩個object會因為實作的windows(XWindows,PWindows)不同而有不同的function, 此時就要動態知道自己是由哪個windows實作才知道自己的method是哪些

![](/assets/images/notes/DesignPattern/4.png)

如圖IconWindow, Transient Window可以透過Xwindow或者PMWindow兩種不同實作方式實作, 而XWindow跟PMWindow細節又有不同, 不同的method, 不同的實作. 因此當我們使用IconWindow或Transient Window時還要注意他是哪種implementation,來確定他有沒有那個method或者那個method實作的細節. 因此我們把abstraction跟implementation分開, 然後abstraction hold他所屬的implementation, 這樣他就知道他有哪些method, 他是由誰實作的, 也可以隨時換另一個實作(subtype)

#### 目的

把一個class的abstraction跟implementation拆開, abstraction hold那個implementation, 需要的時候call implementation的method即可

#### 結果

一個abstract class會hold一個implementation class, implementation class可以有多個不同的實作,有不同的功能, 而abstract class就可以依據hold不同的implementation class來做不同事情,有不同功能, 所以implementation可以達到scalable.

[Reference](http://www.cs.unc.edu/~stotts/GOF/hires/pat4bfso.htm)

### Abstract Factory

#### 簡介

用於當我們的一個object有很多components, 每個components又會因為條件不同有不同的實作方式(Style)時, 為了避免我們寫很多重複的code跟if else判斷(如下),也同時為了避免Main function要提前知道總共有哪些不同實作方式(Style), 不能run-time增加新的實作方式（Style), 所以使用Abstract Factory.

如下,我們的app總共有三種不同components(window,scrollbar, button), 每個components又有兩種不同style, 所以我變成要在app或者main function來看style決定怎麼呼叫, 沒辦法run-time新增style, 而且style一多也很麻煩

因此我們就建立一個MotifFactory跟PMFactory來產生不同style, 一樣用hashmap形式, 這樣就可以動態新增不同style 而且也不用if else.

```
if(style == "PM"){
    window = new PMWindow();
    scrollbar = new PMScrollBar();
    button = new PMButton();
}
else if(style == "Motif"){
    window = new MotifWindow();
    ...
}
```

![](/assets/images/notes/DesignPattern/9.png)

#### 目的

每個abstract factory就是一種components的style(或者說implement方式), 所以我們透過跟abstract factory互動來建立特定的style的components, 而不用在app裡面用if else來透過style variable判斷要建立哪個style的components

注意這個做法是針對style(implementation方式)的abstraction和scalability, 他讓我們可以動態新增不同style, 幫每個components**新增**一個新的style變得比較容易. 但是在create components時就要在另外建立各個style的components, 例如新的components叫做border, 我就要實作Morif, PM的border. 或者我今天要建立新的style 我也要新增多個class來達到這件事情, 只是在new新的style時不用動到app class或者main function, 但麻煩點就是要新增多個class.

#### 結果

Component Style能夠scalable, 能夠run time新增不同的style然後透過abstract factory來產生新的style而不用修改abstract factory或者main function

#### Direct Creation & Indirect Creation

Factory method的特色都是indirect creation, 把object建立這件事deletgate給factory來做<br />
優點是要修改或者擴充這些class時,只需要再新增一個factory跟新的class即可, 不用動到原本的main function之類的

#### Factory & Abstract Factory

- Factory: 一個factory就是固定產出一個class
- Abstract Factory: 每個abstract factory裡面有很多的creation method, 所以一個abstract factory是產出所有這個型別下dependent或related的object, 例如GUI app call一個abstract factory會產生window,scrollbar,screen這樣, 而這些元素都是同個style(同個abstract factory產出同個style)

### Builder Pattern

#### 簡介

當一個複雜的object有多個而且不同的features要設定(例如有些object有變數A,有些有變數B),而且擴充時之後可能有些新的object還會有新的變數之類的, 這時如果我們提供一個abstract level的class有各種的set,get的method能夠讓client自己設定希望的變數的值會非常方便,他們也不用去管後面的constructor怎麼生成這個物件的.

例如像假期規劃, 每個假期可以有不同的餐廳,景點規劃, 如果我們用一般的方法, 我們等於要把這些設定都寫在同一個class裡面, 而且能調的設定都寫死. 要新增或更改設定就要改main function或者要新增一個新的假期行程就等於要再修改main function(如圖,原本只有normal,backpack兩種行程,用if else判斷, 新的行程有新的設定就要再修改main function). 因此我們如果把假期建立這件複雜的事情abstract出來成interface, 這個interface定義了一些set,get method來修改設定, 所以concrete行程都會implement這個interface,然後再視情況自己額外加或更改設定的實作細節. 這樣我們在main function就可以直接透過這個interface來互動, 不用產生一個新的行程就要在修改main function. 新的行程只要implement interface即可.

![](/assets/images/notes/DesignPattern/13.png)

如圖因為不同的行程有不同的vacation class, 這樣會變得非常多非常複雜,main function也要寫很多的if else才能知道要建立哪個vacation class, 要新增新的vacation class又要修改main function.

![](/assets/images/notes/DesignPattern/12.png)

因此這張圖看出來我們把vacation builder abstract起來, main function只需要跟vacation builder互動, 我們不用寫if else來判斷要建立哪個vacation object(透過hashmap即可), 新增vacation class也只需要繼承vacation interface跟寫一個builder繼承builder interface即可

#### 目的

一個複雜的class可能會有不同的設定,然後根據設定不同有很多的concrete class,此時就要在client端用if else來判斷我們要用的是哪個concrete class,要新增新的concrete class也要修改main function. 而且可能新的object除了原本共有的設定外,多了其他設定. 用這個方法我們就統合由一個interface就好, 不用特地跟某個concrete class做互動, 所以main function就不用寫if else來判斷要用哪個concrete class.

#### 結果

我們可以透過builder建立多個不同的concrete class, 使用者不用在main function透過if else來判斷他要建立哪個concrete class,只要透過根builder互動即可, 新增的class也只要有對應的builder就可以輕易達到擴充, main function能輕易的建立新的concrete class而不需要做什麼修改.

### Iterator Pattern

#### 簡介

幫助traverse不同的data structure<br />
例如某個List structure我只要traverse印出裡面的值, 另一個List structure我可能traverse時要做一些判斷來決定要做什麼運算或不同的traverse方式, 這時client不用煩惱這些問題, 全部交給iterator做, 而且要加薪的list structure跟他獨特的便利方式也只需要繼承iterator的interface即可

實際用法會是你每個data structure會有一個createIterator method來return這個structure的iterator, main function或client可以透過iterator這個object的method來traverse data strcuture. iterator本身這個object是要繼承Iterator interface, 要有一些固定的method(first,next,hasNext,getValue...)

![](/assets/images/notes/DesignPattern/10.png)

#### 目的

能夠有一個class幫助我們traverse不同的object array, 做不同的事情<br />

#### 結果

client不用知道iterator implement的細節就可以traverse 各種strcuture. 要加新的list structure也不需要

### Singleton Pattern

#### 簡介

確保該class一次只存在一個object, 不會產生兩個<br />

主要作法就是在class A裡面新增一個這個Object A的variable和一個getInstance的static method, 這個static method會檢查class A是不是已經有建立然後hold住這個object A instance, 如果沒有就產生一個object A instance然後assign給class的variable這個object instance並且return. 下次再call getInstance時就會因為class已經有hold這個object instance就會直接return這個instance而不是create, 保證一次只會有一個instance create.

但要注意的是multithread的問題， 假設兩個thread同時call getInstance, race condition問題可能會同時產生兩個object instance. 因此我們要用java的atomic operation來做getInstance這件事. 

1. 使用**keyword synchronized**, 這個keyword保證一次只會有一個thread進入這個method（這個問題在於這個method的concurrency就很低, 如我已經create好object了,其實就可以恢復multi-thread狀況, 只有第一次create object時會有問題而已)
2. Early creation, 在很早時就先create好, 所以之後thread call getInstance時就不會有race condition的問題, 因為object instance早就create好 (缺點當然是一開始就hold這個instance,會相對浪費記憶體空間)
3. Syncrhonized only the critical block, 類似第一個方法, 只是不針對整個method做syncrhonized,而是只針對creation部分用synchronized, 先檢查完instance是不是null, 是null才會進入critical section, critical section包括在檢查一次instance是否為null,然後才create instance. 這樣這個method就還是可以維持high concurrency

![](/assets/images/notes/DesignPattern/11.png)

#### 目的

確保該class一次只存在一個object, 不會產生兩個

### Composite Pattern

#### 簡介

讓新增小物件(零件)變得很scalable,不需要動到大物件或其他物件的code.

一個composite class可以有多個其他class組成,然後composite class又可以組成一個更大的composite class, 就像是一個tree-structure的建造, 大物件有很多小物件組成, 更大物件有很多東西組成. 這樣的問題是,一但小物件有修改,或新增一個小物件, 很多的大物件都會一起更改到(原本沒那個物件現在要新增), 因此在新增物件方面很不scalable.

主因就是我們把小物件跟大物件當成不同東西來操作對待,他們不是平等關係,明顯的compose concrete class的關係使得小零件在改變或新增時,大物件要跟著變, 所以我們設法讓所有物件平等,不要只有compose concrete class, 而是compose一個interface或abstract class.

用於一個大class(A)是由多個小class(B,C,D)組成, 因此class A會hold List of B,C,D然後依據狀況做操作. 問題是當今天如果要生成一個新的class E到A裡面(即class A由B,C,D,E組成), 這時就勢必要改寫class A

![](/assets/images/notes/DesignPattern/15.png)

圖為initial design, group是寫死他hold哪些concrete class, 不scalable

![](/assets/images/notes/DesignPattern/14.png)

圖為Composite pattern

#### 目的

讓新增小物件(零件)變得很scalable,不需要動到大物件或其他物件的code. 大物件是compose, hold interface而不是concrete class所以能夠動態新增物件.

#### 結果

大物件能夠自由地新增小零件而不用修改到大物件的method. 小物件只要implement interface就可以簡單被大物件compose

### Adaptor Pattern

#### 簡介

用於當我一個物件或資料要給不同的service或class來處理, 而這些class又吃不同的資料型態當成input,因此變成每次有一個新的service或object, 我就要宣告並實作各種不同的資料轉換格式, 才能讓資料給不同的service處理. 會變得非常不scalable, 不相容, 也常常要改object或者service的code.

Adapter就是定義一個class來負責資料的轉移,他會有不同的method能夠把資料轉成另一個形態再去呼叫service, 因此新的格式只要implement adapter的interface就可以擴充新的格式的轉換, 然後service也可以因此不用修改的去跟新的資料格式互動, 新的adapter會直接hold service.

這樣能夠把每個class的功能切得更單純(一個service只負責service,格式轉換的工作給一個專門的class負責), 而且open close法則

Object Adaptor, 圖中表示adapter主要是implement adapter的interface然後composite(hold) service, 這個adapter會實作一個資料轉換的method,把它轉成service看得懂的資料後在使用adapter hold住的service instance的method來得到結果, 所以adapter是implement interface然後hold service

![](/assets/images/notes/DesignPattern/16.png)

Class Adaptor,  adapter同時implement adapter跟service這兩個class, 之後這樣做出來的adapter會同時有interface轉換的功能,也會有service的method<br />
但java沒辦法multiple inheritance可能要改成interface implement的形式

![](/assets/images/notes/DesignPattern/17.png)

#### 目的

將一個interface或data type轉換成另一個interface供應其他service使用, 因為每個service可能input的class type不一樣, 所以資料要做轉換後才能給另一個service用

#### 結果

一個object能夠透過adaptor轉換讓不同的service或class使用


#### Reference

[ithome](https://ithelp.ithome.com.tw/articles/10219666)

### Command Pattern

#### 簡介

假設情境是今天是對button做abstract,可能會有不同功能的button, copy, paste,delete button等等，但實際上是不只button有這些功能，滑鼠右鍵、shortcut key也會有copy,paste,delete這些功能，這樣變成不同的object(滑鼠、shortcut)都要有copy功能就要實作一次。更慘的是，不同的object還要互相合作，例如keyboard copy，滑鼠paste要能夠貼出keyboard copy的東西。

因此我們就把command獨立出來，object(invoker)hold這些command並使用, 而這些command再去hold要作用的對象(receiver, e.g. document), 所以receiver也要做abstraction。

![](/assets/images/notes/DesignPattern/21.jpg)

所以今天這樣只需要一個command class, 然後invoker要use他之前，先設定好要改的document, 接著就invoke這個command, command因為有hold這個document,所以就call這個document的method來完成這件事情。

如圖application透過button或shortcut不同的方式來呼叫copy command, 不用因為invoker不同要在button, shortcut實作兩次copy command. copy command hold editor對editor做修改

![](/assets/images/notes/DesignPattern/20.jpg)

#### 目的

把一個command的使用者跟接收者獨立成兩個layer, 所以同個command能夠用不同的object trigger並且作用在不同的object上。

#### 結果

同一個command我們只要宣告一次，然後他的receiver, 要用時invoker在create、setup。 最後command在invoke自己hold的receiver的operation來完成對receiver的修改。

### Chain of Responsibility

#### 簡介

情境是，對於不同的event我們要做不同的response, 或者同個event但因為裡面的data不同而做不同的request, 為了之後方便增加新的event / response 而不用去修改其他的既存的code(只要寫好event, response的code, 不用去原本的controller新增if/else). 我們會把response, event handler的工作delegate出去，需要時controller去use他即可.

假設把code都寫在main controller(main function)會有以下問題<br />
1. 新增一個event或者權限控管就要新增一個if/else, code extensibility不好。
2. 同上新增一個event/response時，很有可能兩個event做的事情某些地方類似某些地方不同, 造成會有很多重複的code或者整個function/workflow會變得很亂很雜.
3. 例如一個event需要由兩個handler接續完成，現在這種做法會讓code看起來很亂，而且之後要修改handling方式也很麻煩。

![](/assets/images/notes/DesignPattern/18.png)

![](/assets/images/notes/DesignPattern/19.jpg)


#### 目的

繞event handler能夠做好abstraction跟delegation. 每個event handler只處理單一個event. 然後main controller會use event handler來handle event. 這樣要新增event handler時可以很簡單跟原本的code相容而不用去修改原本的main controller之類的. 另外也很適合做event handling的串接, 例如一個event由3個event handler接續來處理這樣.

所以我們的main function會use event handler, 而event handler都會implement這個handler interface, 而且會hold一個event handler(通常是hold其他的handler), 這樣當自己沒辦法處理時，就可以pass給下一個handler來處理,或者處理完後，要交給接續的handler來做之類的。



#### 結果

event handler達到extensibility, 能夠新增event/handler，也能做handler的串接