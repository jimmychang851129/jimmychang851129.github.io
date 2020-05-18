---
layout: post
title:  "Logistic regression"
date:   2020-05-02 21:44:00
categories: Machine-Learning
tags: Course
---

## Machine Learning - Classification

### Probalistic Genrative model

就是貝氏定理的應用，如下圖，判斷某x屬於哪個class就算
P(C|x)，即算已知是x，則x從class1還是class2出來的機率哪個高，作法就是把label的點算mean,variance，就可以求出個別class的高斯分佈，對每個testdata做下述公式。

`if P(C1|x)>0.5 x屬於Class1`
`else x屬於Class2`

![Imgur](https://i.imgur.com/E0j9UBD.jpg)

- **作法**:
1. 找出一個函式
f(C1|x) 給定一個data x函式能夠輸出屬於class1,class2的個別機率(可以用高斯分佈、白努力分佈，看data狀況)
2. 找出mean,covariance使training data分類上屬於自己class的機率最高的值
3. 帶入testing data判斷屬於哪個class

- **Modify**:
按照上述算法，會得到class1,class2個別自己的mean,variance，修改算法是不同的class共享一個variance，若有個別的variance則參數多，容易overfit，所以不同class共用variance來避免參數過多

假設train data有140筆，79筆data屬於class1,61筆屬於class2<br />
$$\sigma$$ =$$\frac{79}{140}$$ $$\sigma_1$$ +$$\frac{61}{140}$$ $$\sigma_2$$<br />
$$\sigma_1$$$$\sigma_2$$是class1,class2個別的variance,$$\sigma$$為修改後的covariance，class1,class2都用這個covariance

- **Naive bayse classification**

上述的方法求出來的是covariance，會有不同feature之間的covariance，如果假設每個feature是獨立事件，那公式可以變成<br />
P($$C_1$$|x) = P($$x_1$$|$$C_1$$) * P($$x_2$$|$$C_1$$) * P($$x_n$$|$$C_1$$)<br />
即算個別feature屬於那個class的機率然後相乘，但效果不好，因為feature間很難是獨立事件

### Sigmoid

下式$$\sigma(z)$$就是sigmoid function，函示圖形為sigmoid function的圖形，可知在z很大及很小時，sigmoid function趨緩

![Imgur](https://i.imgur.com/ySyYWNq.jpg)

![Imgur](https://i.imgur.com/enS1DXS.jpg)


由複雜證明可得
P($$C_1$$|x) = $$\sigma(wx+b)$$ ，w,b算法在上圖

## Logistic Regression(分類)

$$P_{w,b}$$($$C_1$$|x) = $$\sigma(z)$$ w,b為參數 <br />
z = wx+b<br />
$$\sigma(z)$$=$$\frac{1}{1+exp(-z)}$$

![Imgur](https://i.imgur.com/iyX6rjD.jpg)

- **Loss Function**

```
Loss function的先決條件是要可以微分才能做gradient Descent把loss縮小
```

$$f_{w,b}$$ = $$P_{w,b}(C_1$$
|x)，即function是判斷某筆data屬於class 1的機率


| $$x_1$$ | $$x_2$$ | $$x_3$$ |
| -------- | -------- | -------- |
| class1     | class2     | class1     |

L(w,b) = $$f_{w,b}$$($$x_1$$)*(1-$$f_{w,b}$$($$x_2$$))*$$f_{w,b}$$($$x_3$$)
L : 產生x1,x2,x3是這樣值的機率(所以要越大越好)<br />
因$$x_2$$屬於Class2，所以要1-屬於Class1的機率<br />
argmaxL(w,b) = argmin -lnL(w,b)<br />
 -lnL(w,b) = -ln$$f_{w,b}$$($$x_1$$) - ln(1-$$f_{w,b}$$($$x_2$$)) - ln$$f_{w,b}$$($$x_3$$)<br />
 -ln$$f_{w,b}$$($$x_1$$) = ylnf($$x_1$$) + (1-y)lnf($$x_1$$)<br />
故當class=1時 會計算ylnf($$x_1$$)這項，當class=2時(label=0)會計算(1-y)lnf($$x_1$$)這項

```
用square loss即(x-1)的平方這種做法有可能會導致x太大時loss太大
故通常會取sigmoid的方式把輸出值壓小，避免x太大
因此Logistic Regression使用Cross Entropy
```

![Imgur](https://i.imgur.com/xKsSGOM.jpg)

    -lnL(w,b)越小越好

- **Logistic Regression與Linear Regression差別**

![Imgur](https://i.imgur.com/LeraIlf.jpg)

- **gradiant descent**：
    算出最適合的w,b，對-lnL(w,b)做對每個$$w_i$$的偏微分，使用連鎖率

![Imgur](https://i.imgur.com/2CjkGmM.jpg)



## Genrative Discriminative model

| Generative model | Discriminative model |
| -------- | -------- |
| 腦補     | train     |
|直接算出mean,variance|用gradiant descent算出mean,variance|
|data少時可能有幫助|data數量多時用|

## Softmax(於Multiclass classification)

使輸出直加起來為1，在multiclass classification中，每個class都有自己的w,b，把data帶入後算出屬於每個class的機率再取exponential(強調機率大的class)後再做softmax，得到data屬於三個class的機率(此時屬於3個class機率和為1)

![Imgur](https://i.imgur.com/skLsHiA.jpg)

softmax輸出值後在與最後的label data做cross entropy(label data是one hot encoding)

![Imgur](https://i.imgur.com/IfGUvPY.jpg)

## Logistic Regression的極限

下圖這種類型找不到分類法把它分好

![Imgur](https://i.imgur.com/dWT7HRm.jpg)

**解法**：Deep Neural Network，前面幾層layer當作feature transformation，把feature轉成最後一層能做分類的feature