---
layout: post
title:  "Embedded system"
date:   2021-11-11 21:44:00
categories: Operating-System
tags: Course
---

### 甚麼是Embedded System

簡言之，就是一組特殊的hardware以及software組成的一個system，但有別於規格化、規模化的PC，他的硬體通常比較專門，並且需要專門的軟體來跑。

舉例而言，一個主機板的SOC晶片(如BMC)，有著特殊的硬體以及複雜的功能，需要特別的硬體跟軟體(甚至很多時候，一個作業系統) 來達成他的功能。<br />
硬體設備而言，肯定跟我們常見的PC很不同，他把flash, RAM, CPU等等架構都設計在單一晶片上。而且只是板子上的一個晶片，在硬體資源跟設計上肯定跟PC不同。<br />
軟體上需要特定的device driver跟作業系統，因為功能可能複雜到需要一個作業系統配合軟體才能完成，但另一方面為支援硬體，也不見得每個版本linux kernel都可以用(例如firmware, device driver就需要特定的kernel版本後才跑得起來等等)。 
因此，Embedded system的開發

一個系統開發牽涉到硬體與軟體設計，而設計好的硬體後續則會有軟體來負責達成客戶的需求。硬體設計上要盡量能貼近需求，而剩下的則會由軟體來負責完成。

**開發方式**

通常嵌入式系統設計，因為目標硬體通常不像電腦有鍵盤可以做輸入或寫程式，加上target device的資源可能少到無法做程式開發，更多時候是先在host system (e.g. 筆電、主機)上面做好程式開發、編譯，再透過傳輸、燒錄方式把執行檔傳到目標硬體上。<br />
這之中就會牽涉到cross-compile，在host system上開發的程式必須要編譯成在目標硬體上可執行的執行檔。

#### 如何bring-up一個baseboard

bring-up代表讓該硬體晶片配合特製軟體能夠達到他預計的功能。

因為程式主要開發還是在host system, 所以會需要<br />
1. source-code level debugger
2. cross-compile toolkit (把source code編譯成target platform的gcc, linker等等)

在baseboard上需要安裝的東西<br />
1. linux kernel & patch (patch除了bug fix外，也包含對應)
2. root file system, 需要有root file system才能跑起Linux kernel
3. bootloader
4. debug agent (做embedded system上的debug), production不用
5. target program


#### 專有名詞

**init.d**<br />

init process,在bootstrap結束時kernel會呼叫這個process，負責把整個unix系統帶起來，即其他linux所需要的process都會是由init process所fork, 這個binary位置在/sbin/init<br />

init process的config /etc/init.d 或 /etc/rc* 或 /etc/inittab<br />
例如/etc/init.d/cron 裡面就是一個script，定義了cron這個process的跑法跟status，會由init根據init.d/cron裡面的config來fork這個cron process

而init.d裡面本身的file也都是script, 所以其實也可以直接跑，如直接呼叫**/etc/init.d/cron status**，這樣就是跑這個script


init這個process本身也是在disk上面的一個程式，要把他load進來會需要一個file system, 也就是root filesystem

Reference: [csdn:甚麼是init.d](https://blog.csdn.net/cunjiu9486/article/details/109076366)

**bootstrap**<br />

在電腦開機時會分兩個步驟，BIOS跟bootloader, 這整個開機流程就是從電源鍵按下直接到整個OS跑起來的過程。

新型BIOS都是UEFI BIOS, 就是有圖形化的介面，並且可以做很多硬體上的設定，例如Intel VT要不要開啟，風扇狀況等等。<br />
BIOS做的事情就是驗證硬體(e.g. CPU, timer, DMA, MMU), 這個步驟稱為POST (Power On Self Test)
確認完硬體狀況後，就會去disk/ROM找bootloader, 因為PC的儲存裝置很多，所以這部分邏輯也比較複雜，因此猜測PC才會分成BIOS, bootloader兩個階段。相反嵌入式的儲存裝置比較單純，所以就只有bootloader一階段。

在嵌入式系統中，沒有分兩步驟，直接bootloader就把一切做完。可能是因為嵌入式的架構相對單純，不需要設計成兩步驟。

bootloader的工作則是去把找bootImage, 通常bootImage會包含一個rootfs跟一個Linux kernel, bootloader會把他們load進memory並bring-up

**uboot**<br />

一種open-source的bootloader, 通常是embedded system會用的bootloader<br />
另一種常見的bootloader是grub, 通常用於一般PC

相較於grub, uboot有更多的客製化選項跟做法，因此更適合於嵌入式系統，因為硬體的規格很多變的狀況，但uboot也因此學習曲線較深。

Reference:<br />
- [ithome:甚麼是u-boot](https://ithelp.ithome.com.tw/m/articles/10343682)
- [uboot vs grub](https://www.linkedin.com/pulse/unleashing-gatekeepers-u-boot-vs-grub-embedded-mudduluru-ry9ze/)
- [linux讀書會](https://hackmd.io/@combo-tw/Linux-%E8%AE%80%E6%9B%B8%E6%9C%83/%2F%40combo-tw%2FByYcRZjMr)
- [BIOS and bootloader](https://silverwind1982.pixnet.net/blog/post/358594004)

**root filesystem**<br />

跑起整個linux時的file system就是rootfs (即/以下的所有資料夾)，因為user, kernel與儲存裝置互動的方式就是透過file system。

ramfs: 可以擺在ram上面的file system

initramfs: 是一種被壓縮成cpio格式的rootfs, 包含init這個執行檔的rootfs


Reference:
- [rootfs, initramfs介紹](https://flyflypeng.tech/%E5%86%85%E6%A0%B8/2017/06/14/ramfs-rootfs-initramfs%E7%9A%84%E5%8C%BA%E5%88%AB.html)
- [initramfs詳細機制](https://xstarcd.github.io/wiki/Linux/ShengRuLiJie_linux_2.6_initramfs.html)

**Image**<br />

uImage: 可以u-boot的Image<br />
zImage: 壓縮過的image,必須再透過mkimage的binary把他轉成uImage

**Embedded OS**<br />

RTOS裡面通常有sys library (e.g. libc), core kernel, BSP (board-specific package)

BSP為pin腳, device driver, memory配置的Package, 每個不同硬體都會有自己的BSP，這樣才知道怎麼跟該硬體作互動。

**crosstool-NG**<br />

一個能夠產生cross-compile tool chain的tool

### BMC

BMC是一個獨立於CPU、作業系統架構的一個SOC晶片 (i.e.有自己的CPU, memory, disk等等)，用途是讓工程師能夠遠端管理在data center中數以百計甚至千計的server。進行out-of-band management (i.e. 非透過CPU、作業系統等方式來管理、監控硬體資源)

早期工程師可能在作業系統中實作這個監控伺服器狀況的功能，但他的問題點在於
1. 不scalable, 例如硬體壞掉換硬體時，這個系統中的監控程式可能沒辦法很好的handle新的硬體狀況，他也沒辦法知道舊的硬體被換成新的了，而只會記得舊的硬體壞掉的資訊。
2. 如果是壞在CPU, 作業系統等等，這個監控程式就沒有意義，因為他是跑在作業系統上的，這種情況就不能提供任何監控、協助

因此最終做法是在主機板上新增一個SOC，叫做BMC來做out-of-band的監控、硬體操作

其作用內容包括: <br />
1. 監控硬體狀況，例如風扇、CPU、記憶體使用量等等
2. 提供介面給工程師來操控data center裡的server (例如對BMC下指令讓BMC來更新server的firmware, 開關機, 電力更改等等)
3. 如果CPU coredump或hang, BMC能夠存取server的log來debug整個CPU, OS

早期的BMC可能相當簡單，只需要hard-code寫死某些監控程式，但隨著業務越來越複雜，因此逐漸變成在BMC上跑一個RTOS，並執行程式．

BMC硬體上透過System Management Bus (SMBus) 跟硬體互動或Serial Peripheral Interface (SPI)。

#### IPMI

為BMC和硬體溝通的軟體協定，但後期因為沒有統一規格，安全性不夠等等原因，逐漸被redfish取代

#### Redfish

一個使用json, HTTP, RESTful來和硬體溝通的協定，被BMC, hardware廣泛採用來做硬體的溝通，由DMTF維護開發。

相較IPMI，優點在於:<br />
1. json format格式易懂, IPMI是xml格式
2. HTTP, RESTful是廣泛被採用、熟悉的格式來跟硬體互動，也可以讓user透過web形式來讀取BMC資料

現今BMC都會有內建的NIC，所以BMC大多是support Redfish, users, IT就能透過網頁、網址方式來存取BMC的資料以及下指令。
而如果硬體本身也是有support Redfish的hardware, 那BMC跟主機板上的硬體也可以透過Redfish互動。

用法是在BMC的firmware要跑Redfish的service才能夠收發指令。<br />
有Redfish後的BMC, IT, 工程師可以透過網頁介面或者單純發HTTP request到BMC來得到BMC的資料，資料格式也是容易閱讀的json, 比起IPMI，存取資料的方式變得相當簡單。

#### openBMC

以往BMC韌體實做很困難，要根據機器上有的hardware的接口、提供的資訊來決定BMC firmware要怎麼寫才能讀到資訊。只要hardware一換，BMC就要重新編譯，實作support這個新hardware的程式。openBMC就是要解決類似的問題，讓BMC能夠輕易的開源support不同的機器，只要提供config,就能抓取對應的code編譯出可以讀取硬體資訊的firmware。有新的硬體出來，只要有人寫過一次BMC support這個新hardware的code，後人就能乘涼使用。

open source BMC firmware, 讓不同硬體下的BMC firmware都能bring up成功。使用BitBake來針對不同machine type提供不同config來編譯BMC firmware跟bring up BMC。讓BMC在不同的機器下都能順利讀到各個硬體的資訊。