---
layout: post
title:  "CPU Scheduling"
date:   2021-03-01 21:44:00
categories: Operating-System
tags: Course
---

## CPU Scheduling

### Background

現在OS都是multiprogramming,一個OS會跑很多process,可能也有很多users產生很多Process<br />
但我們只有少量的core來處理這些process, 因此就要輪流執行, 要怎麼有效分配資源讓每個process都能得到合理的CPU資源使用量

- Long Term Scheduling: 考慮長期電腦能跑的process數量
- Short Term Scheduling: 考慮當下資源要如何合理分配給各個資源

scheduler觸發(active) 開始執行的時間點(就是要分配資源的時候)<br />
1. Context Switch時(Process Timeout或者要進行IO時)
2. Interrupt發生
3. 新的process產生或者舊的process結束


### Criteria for CPU scheduling algorithm

判斷這個CPU scheduling的演算法的好壞的幾個基準, 彼此之間是trade-off, 很難同時滿足所有的基準<br />
因此要根據使用用途決定scheduling方法

- CPU Utilization: 看CPU資源有沒有用好用滿
- Throughput: 一段時間完成的process數量
- Turnaround Time: 把一個process從init到terminate所花的時間(包括waiting時間)
- Waiting time: 總共的waiting time
- Response time: 一個process從ready state到下個IO request(進入ready state)的時間

### Scheduling

#### Assumption

- 一個process只有一個user
- 一個process只有一個thread
- process間沒有共用資源

#### First Come First Served (FCFS)

先來的process先佔據CPU直到他做完<br />
如果某process要做IO就給第二早到的process來用CPU,第二早到的process用完後還給最早到的process

缺點:<br />
- short job會等很久, average wait time很久
- 很容易IO device idle,或者DoS

#### Round Robin

在time-sharing系統常見, 不會有starving或者DoS問題, 每隔一段時間就會輪到每個process<br />
不能把time-slice設太大, 不然就是一直在等,可能變成FCFS, 但設太小會有太多context switch(high overhead)

#### Shortest Job First (SJF)

選擇最短的工作做, 可以optimize wait time跟throughput<br />
缺點是不公平, IO bound jobs會花較少的CPU time, 所以priority都會比較高, 用很多CPU的process就會很難輪到執行導致starvation<br />
此外, 因為這樣的排程,每個process的CPU time會變得無法估計

#### Multilevel Feedback Queues(MLFQ)

使用之前的訊息來預測之後的task scheduling, job priority

使用多個queue,每個queue代表不同的Priority,越上面的queue裡面的task priority越高, time slice越小. 越下面的queue裡面task priority越低,但time slice越大<br />
每個queue做round robin上面的queue做完才換下面一層queue做 (當然還是會遇到starvation)<br />
因此每個task timeout後往後移到下一層的queue. 因此IO bound job會在high priority queue, CPU-intensive會在low priority queue. IO很多的task在timeout前就會context switch(因為要做IO),因此不會expire

high priority queue放會有很多IO的task, low priority queue放CPU-intensive task