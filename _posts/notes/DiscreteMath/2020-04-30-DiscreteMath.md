---
layout: post
title:  "Discrete Math"
date:   2019-04-30 21:44:00
categories: Discrete-Math
tags: Course
---

## Discrete Mathmatics

### 好讀版

因為jekyll在latex應用方面較為麻煩，所以附上hackmd連結，方便閱讀

[https://hackmd.io/I_GuiWB-TS-i5sYU2Rrk1A?view](https://hackmd.io/I_GuiWB-TS-i5sYU2Rrk1A?view)

## GCD

### 特性

a,b,k $$\in$$ Z
1. gcd(a,b) = gcd(a+kb,b) 蠻直觀的
2. gcd(a,b) = gcd(b,a%b) => Euclidian步驟

### Extended Euclidian Algorithm

a,b $$\in$$ Z
存在x,y屬於Z
ax+by = gcd(a,b) 透過十字交乘法(Euclidian)

故gcd(a,b) = 1時，存在x,y $$\in$$ Z
ax + by = 1 

### Relative prime

每個整數可以質因數分解，而且是唯一
p is prime
p | $$a_1a_2...a_n$$
then p | $$a_i$$ for some i

proof : 
p | ab then p | a or p | b

## Ring

### Definition

定義Ring(R,+,x)，符合以下條件
1. 加法交換、結合律
2. 有additive inverse,additive identity(-a,0)
3. **有乘法結合律、分配律**
4. **注意ring的乘法不一定要有乘法的交換率**
5. **Ring不一定要有multiplicative identity(unity)**

![](https://i.imgur.com/M5aVnkQ.png)

#### commutative ring

若ab = ba

#### proper divisor of zero

b is a proper divisor of zero if
1. a + z = 0  $$\forall a \in$$ R
2. ab = 0 $$a,b \ne z$$

`即a,b相乘為0但a,b都不是additive identity，如矩陣就有此特性`

#### Ring with unity

unity(multiplicative identity) : 我們說一個數字u是unity如果<br />
au = a, $$\forall a \in R$$<br />
有unity的ring叫做ring with unity

#### multiplicative inverse

先決條件：此ring要有unity
令b為a的multiplicative inverse, 則 ab = u, a則叫做unit

#### integral domain

定義：某ring為commutative with unity，但沒有proper divisor of zero
可以想成就是在講一般整數的狀況

#### field

定義 : ring為commutative with unity，但每個非0(additive identity)的數a都是unit(都有multiplicative inverse)

#### subring

R(R,+,x)，令S $$in$$ R，即S內的元素R都有，且S的+,x運算結果都在S內，不會算出一個b $\notin$ S，則S為R的subring

![](https://i.imgur.com/8kWVmLF.png)

`證明方法為已知S的set內的元素且運算規則跟Ring R相同，檢驗S是否符合Ring定義的六個規則，符合的話就是subring，且符合上述兩點的話保證一定會符合Ring的六個規則`

### property

括號部分為證明的方法

### additive identity is unique

1. additive identity is unique(用反證法)
2. based on 1, additive inverse is also unique

### Concellation law of addition

1. a + b = a + c then b = c(等式左右加上a的inverse)

### multiplication of z

1. $$\forall a \in R$$ az = za = z(令z = (a-a)代入)

### unity is unique

1. if Ring R has a unity, then it is unique(同第一點證法)
2. Since unity is unique, multiplicative inverse is unique

### Field $$\in$$ Integral domain

Integral domain說明若ab=z其中必有一個為z(additive identity)
Field說明每個a $$\ne$$ z 為unit(有multiplicative inverse)
因此只要討論Field是否無proper divisor of zero
令ab = z 有兩種情況

1. a or b = z，此情況符合Integral domain
2. a and b $$\ne$$ z<br />

`ab = z`<br />
$$a^{-1}$$ab = $$a^{-1}$$z, 根據上述第三點任何數乘z等於z<br />
then b = z

## Module

### 定義

a is congruent to b modulo n
$$a \equiv b \pmod{n}$$ ， 也就是說存在 $$k \in Z$$
a = nk+b

### 特性

$$a \equiv b \pmod{n}$$, $$c \equiv b \pmod{n}$$，<br />
a,c有相同的餘數，則(a-c) | n , a-c整除n

if $$a \equiv b \pmod{n}$$ then<br />
$$a+c \equiv b+c \pmod{n}$$<br />
$$ac \equiv bc \pmod{n}$$<br />
$$a^c \equiv b^c \pmod{n}$$<br />

## Ring of modulo

R($$Z_n$$,+,x)是一個ring
[0] = {...-2n,-n,0,n,2n...}
[k] = {...-2n+k,-n+k,k,k+n}
1. 令$$Z_n$$ = {0,1,2...n-1}，即 $$Z_n$$為一個0到n-1的set
2. + : $$[a]$$ + $$[b]$$ = $$[a+b]$$
3. x : $$[a][b]$$ = $$[ab]$$

### 特性1 : commutative ring with unity

R($$Z_n$$,+,x)是一個commutative ring with unity [1],additive identity [0]

### 特性2 : $$Z_n$$ is a field in n is prime
Field  : Ring with that of every non-zero element is unit

> proof : 存在s,t$$\in$$ Z，as + bt = 1 then gcd(a,b) = 1，此公式可用Euclidian推得<br />
因此 $$as \equiv 1 \pmod{b}$$ , 證明a是unity<br />
所以回到原case，如果gcd(a,n) = 1則保證a一定是unit

## Group

G(G,o)比ring更簡單，只有一個operator
1. 封閉性、結合律
2. Identity 和 inverse(所以group內元素都要跟modulo是互質，不然沒inverse)

![](https://i.imgur.com/XJzMowM.png)

**注意 Z,R,Q,C在乘法operator沒有一個是Group因為 0 沒有inverse!!!**
**在乘法下,$$Z^*$$,$$R^*$$,$$Q^*$$,$$C^*$$才是Group(without 0)**

$$Z_n^*$$ = {i} $$\forall$$ i < n and i is coprime to n}  
### abelian group(commutative group)
就是有交換率的group

### 特性

1. Group的identity,inverse是unique，證法跟ring依樣
2. 有left,right cancellation<br />
`ab = ac then b = c`

### subgroup
跟subring一樣，subgroup H 的element為G的subset，然後operation符合Group的規則
或者符合以下3個規則
1. H $$\in$$ G
2. a,b $$\in$$ H, ab $$\in$$ H
3. a $$\in$$H, $$a^{-1} \in$$ H
想法相同，符合這三個條件，就會符合Group的所有定義規則

## Homomorphism

### 定義

Group和Group的mapping<br />
令(G,o),(H,+),存在函數f: G-->H<br />
函數f代表定義域為G，值域為H，如 a $\in$ G，則f(a) $\in$ H。

### 特性

1. f($$e_G$$) = $$e_H$$, identity要互相對應
2. f($$a^n$$) = $${f(a)}^n$$
3. S是G的subgroup,則f(S)是H的subgroup

proof 1:<br />
f($$e_G$$) * $$e_H$$ = $$e_H$$

## isomorphic group

Group G,H除了事homomorphic外，更要是一對一(one-to-one)且映成(onto)

## Cyclic group

$$g^{|G|}$$ = I mod G  肯定的，只是可能order不到G，重複很多次
即Group內有一個元素x,所有元素都可以用x的運算表示，例如(G,+)所有元素都可以用nx來表示，(G,*)所有元素都可以用$$x^n$$表示。

Group (G,+)裡存在一個元素x,使得 $$\forall a \in G$$, $$a = x^n$$

EX : H($$Z_4$$,+) => [3]就可以產生所有元素,[3],[2],[1],[0], H = <[3]>

if G has generator then G = $$2,4,p^k,2p^{k}$$<br />
if G has generator, then the amount is $$\phi(\phi(n))$$<br />
if a is generator then $$\forall i, gcd(i,\phi(n)) = 1$$ $$a^i$$ is generator

### order of a

O(G)，|G|，G為group的話，就是Group的元素總數
O(a)，a為element的話，a的運算所能表現的數，即|\<a>|
O(-1) = 2, O(1) = 1

### Lagrange theorem

G 是一個n的group，則G內每個元素k的order ord(k) | n , n = <br />
可以證明euler theorem

### 特性

O(e) = 1, identity運算能表現的數只有自己<br />
if |G| = infinite, G is isomorphic to (Z,+)<br />
if |G| = n, G is isomorphic to ($$Z_n$$,+)<br />

### Fermet little theorem

for any group皆成立，因為group內元素都有inverse，所以保證元素和p互質<br />
p is prime and a,p are relative prime<br />
then<br />
$$a^{p-1} \equiv 1 \pmod{p}$$<br />

proof : <br />
since gcd(a,p) = 1<br />
存在x,y 使 ax+py = 1<br />
$$a \equiv 1 \pmod{p}$$<br />
又 $$ma \equiv m \pmod{p}$$，故不同的ma就會生成一個m，所以gcd(a,p)為1時，a可以為cyclic <br />group的generator，因此pa就是把a繞過一遍回到a， p-1就是identity

### feature of generator

只有$$Z_n$$， n = 2,4,$$p^k$$,$$p^{2k}$$有generator<br />
如果a是generator則 $$\forall i \perp \phi(n),a^i$$也是generator<br />
若$a^i$是generator則$$\forall p | \phi(n), a^{phi(n)/p} \neq I mod n$$

## Polynomial Ring

### definition

就是一個多項式他的每項係數都屬於Ring R，然後這種多項式的所有組合形成一個ring R[x]，此Ring的set就是多項式
Ring(R,+,\*)
$$a_0,a_1...a_n \in R$$
R[x] = $$a_nx^n+a_{n-1}x^{n-1}+...a_0$$
R[x] = G[x] 就是比較兩個polynomial相不相同

假設Ring有n個elements, polynomial有k項
R[x]總共有$n^k$個elements

有三種運算方法
1. 正常多項式運算
2. 多項式運算後係數mod p
3. 運算後的多項式除以一個irreducible的多項式

irreducible polynomial:不能被拆成次方更低的polynomial

### 特性

1. R is commutative => R[x] is commutative
2. R is a ring with unity => R[x] is a ring with unity

### Reducible

把Ring，Group那一套搬到多項式來

f(x) $$\in$$ F[x], F is a field(F是一個field,用這個field的element建造出一個polynomial的set F[x])<br />
f(x) is reducible(可被拆成兩個多項式)<br />
g(x),h(x) $$\in$$ F[x], f(x) = g(x)h(x) if <br />
1. deg(f(x)) > 2
2. f(x)的根 $$\in$$ F

### Euclidian for polynomials

就是polynomial的Euclidian<br />
f(x),g(x) $$\in$$ F[x], deg(f(x)) < deg(g(x))<br />
then<br />
g(x) = f(x)q(x) + r(x) where deg(r(x)) < deg(f(x))<br />
f(x) = r(x)$$q_1(x)$$ + $$r_1(x)$$ where deg($$r_1$$(x)) < deg(r(x))<br />
用上述方法得gcd(f(x),g(x)) = 1 則f(x),g(x)互質<br />

### Congruent in polynomial

s(x) $$\in$$ F[x]<br />
定義H[x] = F[x]%s(x)<br />
[f(x)] + [g(x)] = [f(x)+g(x)]<br />
[f(x)] * [g(x)] = [f(x)g(x)] = [f(x)g(x) mod s(x)]<br />

if s(x) is reducible, H[x]是一個field<br />

## Galois field

polynomial ring的算法就是兩polynomial做運算，係數mod特定數字，這樣不能保證內部每個元素都有inverse
所以Galois field就是polynomial ring運算後再mod一個irreducible的polynomial，這樣就會變成一個field:galois field<br />
$GF(2^n)$ : n-1次多項式，係數只能是0 or 1，mod一個n次多項式(irreducible)，加法就是xor，乘法就是shift + xor<br />
$GF(p^n)$ : n-1次多項式，係數小於p，mod一個n次多項式....

### Quadratic residual
x*x = a a是quadratic residual，在finite field裡<br />
如果n = p，b是generator，a是QR iff $$a=b^i$$，整個group/field裡有(p-1)/2個QR<br />
n = pq，a is QR iff a $\in$ Q(p ) and a $\in$ Q(q) Q(p )*Q(q) = (p-1)(q-1)/4


## Euler phi function

### 定義

$$\phi(n)$$ = {x where 1 $$\leq$$ x $$\leq$$ n & x $$\perp$$ n}<br />
$$|Z_n^*|$$ = $$\phi(n)$$ if n is a prime


### 特性

$$\phi(p) = p-1$$<br />
$$\phi(p^k) = p^{k-1}(p-1)$$<br />
$$\phi(mn) = \phi(m)\phi(n)$ $if m $$\perp$$ n

#### proof

##### proof 2

$$p^k$$ 有{p,2p,3p...$$p^{k-1}p$$}個數不行，
所以總共是$$p^k$$ - $$p^{k-1}$$數與p互質

##### proof 3

a $$\perp$$ mn iff a $$\perp$$ m and a $$\perp$$ n，
故與mn互質得數必和m互質也和n互質<br />
令a = ni + j，只要j$$\perp$n則a$$\perp$$n<br />
故令a = {j,n+j....(m-1)n+j}必和n互質，對於每個j有$$\phi$$(m)個數<br />
故總共有$$\phi(n)\phi(m)$$

如果 $$i_1$$n+j = $$i_2$$n+j mod m，透過消去得知$$i_1$$n = $$i_2$$n mod m，因為n$$\perp$$m 且$i_1,i_2 < m$$ 故$$_1$$ = $i_2$$。

### Euler phi function

![](https://i.imgur.com/eihL9vl.png)

### Euler theorem
if a $$\perp$$ n，即(a|$$Z_n^*$$) 且 a>n then $$a^{\phi(n)}$$ = 1 mod n，跟費馬小定理一樣
$$\phi(n)$$ = {$$c_1,c_2..c_{\phi(n)}$$}
$$ac_1ac_2...ac_{\phi(n)} \equiv c_1c_2...c_{\phi(n) \pmod{n}}$$
因$$c_i \perp n$$故可以抵銷掉得到 $$a^{\phi(n)} \equiv 1 \pmod{n}$
!!! 在group內 $$c_1 \perp n$$才會有 $c_1$$的inverse，所以不能是所有1~n-1數字拿下去乘

<!-- ## 簡介
### key長度與維持多久:
64 bits : hours/days
128 bits: months,years if no quantum computer
256 bits: years even if there is quantum computer -->