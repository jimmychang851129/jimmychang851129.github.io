---
layout: post
title:  "Little Endian"
date:   2021-06-20 21:30:00
categories: ctf
tags: "Course"
---

因為在練習pwn時，每次都會搞混little endian實際上寫入的方法，或者stack, gdb之類的表示法，常會搞混到底要怎麼看字串或integer的順序，所以紀錄一下關於little endian或者Stack寫入、allocate的筆記。

### Little Endian & Stack address

我們以以下程式為例, 一開始在main function接著呼叫fun function

```
void fun(){
    char data[32];
    int t;
    scanf("%s",data);
    scanf("%d",t);
    return ;
}

int main(){
    int a,b;
    char s[8];
    fun();
    return 0;
}
```

如下圖，gdb中看stack資料時，他的memory address是如下的安排，當我們進入新的function，allocate一塊新的資料時，stack的address是往下長的，往低位allocate空間，所以呼叫越多function，或遞迴越深，memory address的值就會越來越小。

![](/assets/images/notes/CTF/littleendian/1.png)


但是當我們寫入時，我們是由rsp的位置往rbp寫，所以是由低位往高位的位址寫(合理吧，這樣才能做bof，往低位寫怎麼蓋得到rbp, return address)。 所以雖然呼叫新的function，空間是往低位allocate，但是我們在Stack frame裡面寫入時，是由低位往高位寫。 如下圖，我們輸入a~x的字串，我們會是從0x20往0x30寫，所以實際上在memory layout是往上寫，但在gdb裡很confused的就是他的排版看起來是往下，但配合左邊的memory address就會知道他確實是往高位寫。

而下圖的範例是採用Little Endian的格式，Little Endian是現在x86, 大部分arm, risc-v採用的儲存資料在記憶體的格式，即數值中低位的資料會寫在memory address低位處，而資料中高位資料會寫在memory address高位處<br />
Big Endian則是數值低位寫在memory address高位處，而資料中高位資料寫在寫在Memory address低位處。

所以以這個例子而言，abcd...vwx， a是起始位置，x是終點位置，所以理論上a是LSB(Least significant byte)而x是MSB(Most significant byte)。所以a要放在low address而x要放在high address。因此就如我們這張圖看到的例子，在gdb上呈現，我們看到的會是倒著的字串，因為8個bytes的alignment中，右邊是low address而左側才是high address，然後他會是往高位址寫。

![](/assets/images/notes/CTF/littleendian/2.png)

實際例子如圖，我們在fun這個function input "a-zABCDE"這個字串，在gdb上面的排版，一行memory address裡，右側是低位，左側是高位，所以看到的一行8個byte的layout會是右側是low address,左側是high address, 然後右邊的8個byte又比左邊的8個byte更high address。，<br />
雖然gdb看起來是往下寫，但那是因為他的呈現方式，下面是high address，所以他其實是往高位寫，然後我們可以看到我們字串存的方式是同一行，先寫在左邊8 byte在寫右邊8 byte, 8 byte的block中是由右寫到左，整體是由低位往高位寫。

![](/assets/images/notes/CTF/littleendian/3.png)


#### 整數資料

以整數的資料存放而言，結論就是看起來就會跟我們預期的效果一樣。如下圖實際例子，我們在fun函式裡面輸入0x123456的字串後，真正存放在stack(0xe71c)，上面的資料形式也是0x123456，不像String一樣會有反方向的問題。

![](/assets/images/notes/CTF/littleendian/4.png)

原因是因為0x123456而言, 0x56是低位而0x12是高位，所以像下面圖例，最右側是低位而往左側越來越高位，我們存放就會是由左而右0x12,0x34,0x56。跟我們一般習慣看的順序相同。

![](/assets/images/notes/CTF/littleendian/5.png)

### Word & byte

- word: 2 bytes
- DWORD: 4 bytes
- QWORD: 8 bytes

讀取資料會以當前的address在往高位讀取特定數量的byte。<br />
所以例如讀取 PTR WORD [0x31]，會是讀取memory address 0x32,0x31這兩個byte, 假設mem[0x32]= 0x12, mem[0x31] = 0x34，則讀到的integer會是0x1234,讀到的String會是0x3412,反正mem[0x31]存的值是低位，看要用什麼datatype來存取他

### rip address

有時在寫shellcode或者實際上有時看到組語會看到[rip + 0x30]之類的address，這部分很容易搞混，因為這種寫法rip都不會是當前顯示在gdb上的那個rip值，這種access方式的rip都是下一個指令的位址。
所以假設如下

```
0x30: mov rax [rip+0x30]
0x35: nop
```

此時memory address 0x30那行的指令，[rip + 0x30]的值不會是0x60，而會是0x65。此時rip要帶入的值會是下一行指令的memory address。

範例為hsctf 2021的gelcode，手寫shellcode題，如下圖，此時指令會把[rip+0x30f]位址的值加進ecx，理論上應該會是0x55ae2d09a289(rip) + 0x30f，但會發現我們這樣加起來的值會是0x55ae2d09a598，而他右邊comment的值卻是0x55ae2d09a59e，原因是此處的rip值不是現在這個command的rip值，而是下一個command的rip值，所以是0x55ae2d09a28f(add al,0x1)這行。這樣加起來才會是正確值。

![](/assets/images/notes/CTF/littleendian/6.png)

### Materials

- [gdb memory address command](https://sourceware.org/gdb/current/onlinedocs/gdb/Memory.html)
- [guide to x86 assembly](http://www.cs.virginia.edu/~evans/cs216/guides/x86.html)
