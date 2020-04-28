### Semaphore

一種generalized的lock,有一個整數變數和兩個atmoic指令能夠update他的值. Semaphore的值代表他有的資源數目,當值變成0代表沒有資源了,這時在要資源(wait)就會block. 當資料用完會呼叫signal, 這時semaphore值會+1

功能:<br />
- Binary Semaphore(Mutex): 基本上跟lock一樣, 只有兩個值(就是有lock只能有一個人把lock設成1來讀取資源)
- Counting Semaphore: 多個resource可以分配時, 有n個資源, semaphore的初始值就是n, 只要semaphore值不等於0,process就可以取得資源
- Schedule Constraint: 第三個功能, 用於確保code執行順序, 保證例如Thread A的code 2比Thread B的code2早執行

Semaphore本身有一個queue存等待資源的process
每個process都會執行S.Wait(), 如果Semaphore值不等於0就直接assign資源, 不然就放進queue. 用完資源的semaphore會呼叫S.Signal 然後就會pop queue裡面的一個process出來用, Signal和wait都要是atomic

另外lock的狀況process不能重復要, 但semaphore就只是一個紀錄, 所以process A可以call多次的wait, 那就代表資源pool有複數個資源給process A使用

一般lock的implementation都是使用busy waiting或者disable interruption來達成atomic operation, 避免鎖到一半context switch或者被interrupt

#### Instruction

- S.Wait(): 等到semaphore S能用
- critical section: 在wait和signal之間
- S.Signal: 要釋放資源的process發送signal告訴其他process資源可以用了

#### Usecase

1. process之於core, 電腦有4,8個core能夠跑, 這時就能用semaphore來決定process是否能用core, 這樣就不用4個lock
2. Webserver, page request送給其中一個worker thread來負責做事(event handler threat給worker thread), 所以用semaphore紀錄資源(n個worker thread就開semaphore of value n)
3. Mutual Exclusion: 把semaphore設成1, Wait, Signal之間就是critical section,一次只允許一個code執行那段, 或存取那段資源
4. Schedule Constraint: 將semaphore設成0, 目標是避免因為context switch導致執行順序有差異, 我們希望某些code是依據某個固定的順序執行, 此時就可以用這個. 因此semaphore設成0, 在我們要保證較後面執行的程式碼前放S.wait(), 這樣就算他執行到也會先block. 直到我們希望先執行的程式碼執行完後,他呼叫S.Signal, 這時wait的程式碼就可以執行. 所以她不像lock的用法. 純粹是為了保證execution order的方法

```
EX: Schedule Constraint
我們希望保證執行順序 A(2) -> B(3) 儘管有OS scheduling
設Semaphore = 0
Thread A: 1 -> 2 -> 3
Thread B: 1 -> 2 -> 3
因此我們在A(2) -> A(3) 之間加入Signal, B(2) -> B(3)之間加入Wait
這樣在執行時就可以保證A(2)一定會比B(3)早執行
```

Schedule Constraint當有n個thread時也可以這樣做,只是要修改一下(例如每個thread都要wait, 把Semaphore設成-1, 或者多個Semaphore)

### Monitor

Semaphore的缺點:
- 可能一個小型的程式或application, 可能會需要很多的Semaphore, 然後整個code的邏輯就會很複雜, 例如semaphore的初始值代表甚麼意思(不像lock就清楚)
- Semaphore跟protected data的關係有時候是unclear, 會讓人不知道semaphore確切要保護的東西是啥

Monitor是一個Class, 內建同步的method (Mutual Exclusion). 所以很多的同步問題我們就不用自己在那邊lock, semaphore, 可以交由Monitor來幫忙. 而Monitor Class裡的private data就是要保護的資料 (又或者說Monitor內只能有private type的data(public就保護不了不能保證同步啦))

Montior class裡有:<br />
- 一個lock: 用來保證只有一個thread在critical section中執行(程式特性, 不用特別宣告甚麼, clas內method宣告synchronized即可,基本上意思就是這個class是critical section,呼叫這個class的method需要lock,所以要呼叫這個class的method是mutual exclusive)
- condition variable: 能夠讓thread在critical section內sleep而且release lock

#### Condition Variable

Condition variable原因: 可能今天某個thread執行Monitor class內的method結果某個運算需要等, 那這個thread就在這個class內sleep等到某個event trigger再叫醒他去執行(例如Monitor class的method要acquire其他的lock, 那就要等到其他人釋放), 但這時thread在睡覺時順便把整個Monitor Class lock住了, 其他人根本沒辦法呼叫Monitor class的method. 所以設定這個變數讓上述狀況發生時, thread會把Monitor lock release讓其他人用

因此,在呼叫Condition Variable時, thread一定要有lock

Condition variable是一個queue of thread waiting for events  inside a critical section, 他有3個Operations<br />
- wait(lock): atmoic, release lock然後sleep, 當thread醒的時候再重新要lock
- signal: 把queue裡面的一個thread叫醒, 如果queue是空的就啥都不做
- BroadCast: 把所有queue裡的thread都叫醒


兩種不同的condition variable的形式:<br />
- Mesa: Condition variable在signal時會把queue裡面的thread叫醒然後"試著"給他lock而不是指定把lock給某個thread. 但有一個可能性是在叫醒的同時,剛好有其他thread在執行也要lock然後成功要到了. 這樣的話被叫醒的那個thread就要不到Lock就要回去睡覺QQ. 這種狀況下的implementation, wait()這段code要用while, 因為不能保證叫醒一定能拿到lock
- Hoare: Condition variable在signal時指定會把lock給特定的thread, 這樣wait時就不用跑while loop, 因為被叫醒的那個一定會拿到lock

```
EX:
while(!queue.empty()){
    wait();
    remove and return item
}
```

Monitor不是thread, 所以它是mutual exclusive, 假設固定數量的wait signal, monitor會因為這些指令順序不同而有不同結果, 但semaphore不會(因為會記錄). 例如Monitor如果call signal但queue是空的, 那這個signal就浪費了.<br />
Semaphore某方面來說會存目前資源的state,狀況, Monitor這種lock不會