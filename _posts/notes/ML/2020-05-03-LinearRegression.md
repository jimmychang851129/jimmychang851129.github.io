---
layout: post
title:  "Linear Regression and Grandien Descent"
date:   2020-05-03 21:44:00
categories: ML
tags: Course
---


## Linear Regression

- **作法**
    1. 設定好model(取哪些feature，幾次方程式)，決定feature的scale的參數叫weights
    2. 找Loss Function(和label值得rmse,mse之類的)
    3. gradiant Descent
    4. 可以不同的data用不同的function(如神奇寶貝伊布的進化可以自己獨立一個函式，次方數根general的神奇寶貝不一樣)
    5. Regularization：使曲線變平滑，不容易受到noise、極端data影響，且weights較固定，不會因為training data的一點變化導致weights變化太多)、較不會overfit原本的data

- **Gradiant Descent**:
    1. 畫出weights和Loss的二維函式圖形
    2. 算每個weights值得斜率，斜率為負的則增加weights，斜率為正則減少w

        $$w_1$$ = $$w_0$$ - L.$$\frac{dL}{dW}$$

        L : Learning rate

    3. 問題：
        1. 可能卡在local minimum
        2. 在微分=0的地方幾乎不動
        3. 斜率越大動越多，導致在山峰間跳來跳去之類的，到不了底部(也跟Learning rate有關)


![Imgur](https://i.imgur.com/S3OtolO.jpg)

## Validation

- **cross validation**：把training data切成training data和validation data，再用切完後的training data train並用validation data來驗證自己model的正確率，取validation_loss最小的model，再拿整個training data train一次來predict testing data，避免直接拿整個training data訓練導致overfit

![Imgur](https://i.imgur.com/4echmIk.jpg)

- **N-fold validation**:把training set切成N塊其中一塊當作validation set，其他來train再換不同的train set當作validation set這樣做N次，平均後取valid_loss最小的model再拿整個training set來訓練，下圖為3-fold cross validation

![Imgur](https://i.imgur.com/jM5uhb7.jpg)

## Gradiant Descent

![Imgur](https://i.imgur.com/RPVUrMe.jpg)

- 當gradiant為負時，weights加、當gradiant為正時，weights減
- 改良:
    1. **Adaptive Learning rate** : $$L=\frac{L}{\sqrt {t+1}}$$
        - t = gradiant descent次數，故越後面的learning rate越小，剛開始最大
    2. **Adagrad** : $$w^{t+1} = w^t - \frac{L^t}{\gamma^t}g^t$$
        - $$w^{t}$$:第t次gradiant descent後的weights
        - $$L^{t}$$:第t次gradiant descent後的Learning rate
        - $$\gamma$:之前所有次gradiant出來的值的root mean square
        - $$g^t$$:第t次算出來的gradiant值， $$g= \frac{dL(W^t)}{dW}$$
        - 即Loss function(L)在帶入參數(w)後對各個weights的偏微分

- **stochastic Gradiant descent**
    - 介紹:不拿整個data來做gradiant descent，把training data切成batch來做gradiant descent，例如切成三份，即在對整份data跑完一次，就做了3次gradiant descent，雖然每次gradiant descent步調較亂，但是更新速度較快

- **Normalized**:幫助更快速達到minimum，因為每個feature分佈是標準化過後的，沒有那種值特別大的，而且標準化後值分佈較集中

