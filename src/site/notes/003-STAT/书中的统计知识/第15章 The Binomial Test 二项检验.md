---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第15章 The Binomial Test 二项检验/","dgPassFrontmatter":true}
---


## 1. Introduction

binomial test，response为结局二种任一的outcome（`其中两种结果机率相同`） 
临床试验中，使用二项检验，`评估结果发生率`。用到的两种类型的检验方法，为McNemar's test和 sign test（本章后边）    

## 2. Synopsis
假设`数据为二分类数据，事件的概率由p(0<p<1)表示。验证总体比例p是否与假设值p0是否不同`。  
n个观测，每个观测本身有两种结局（事件发生-events，事件未发生-no events），将事件发生的总数记为X，X/n可表示p  
综上，H0：X/n, should be close to p0, i.e., `X should be close to n · p0  `

![../../Z appendix/Pasted image 20230216173104.png](/img/user/Z%20appendix/Pasted%20image%2020230216173104.png)  

SAS function PROBBNML can be used to determine XL and XU  

## 3. Examples

如15.1例子：一种治疗xx病的产品，XX药在普通人群中的已知治愈率为40%。一项对25名患者使用该产品治疗的研究中，患者还服用了高剂量的维生素C。14名患者被“治愈”。这（服用VC）与普通人群（不服用VC）的‘治愈率’（40%）是否一致

![../../Z appendix/Pasted image 20230216173145.png](/img/user/Z%20appendix/Pasted%20image%2020230216173145.png)

### 3.1. 方法一：粗滤估计，此时的p不是很准确

![../../Z appendix/Pasted image 20230216173158.png](/img/user/Z%20appendix/Pasted%20image%2020230216173158.png)

![../../Z appendix/Pasted image 20230216173227.png](/img/user/Z%20appendix/Pasted%20image%2020230216173227.png)


### 3.2. 方法二，使用Normal Approximation--正态近似（n较大，且非极端p，即接近0.5）

 X, can be approximated by a normal distribution with mean n⋅p and variance n⋅p⋅(1–p).--》当n极限大时，p值接近0.5时，效能更好

![../../Z appendix/Pasted image 20230216173241.png](/img/user/Z%20appendix/Pasted%20image%2020230216173241.png)

![../../Z appendix/Pasted image 20230216173255.png](/img/user/Z%20appendix/Pasted%20image%2020230216173255.png)

#### 3.2.1. 使用SAS分析，也是基于使用Normal Approximation--正态近似（n较大，且非极端p）

令 yi 表示患者 i (i = 1, 2, ..., n) 的二项式响应，yi数值为 0（“非事件”）和 1（“事件”）
请注意，X 是 n 次试验中的“事件”数，是 yi 的总和。 
“事件”的概率 p 是从中选择 yi 的分布的总体平均值（0+1+1+0+0）/n。p作为样本均值，其遵循正态分布  
test:p=p0

```sas
proc freq data=gwart;
    tables cured/binomialc(p=0.4) alpha=0.05;
    /*binomialc：使用Normal Approximation，进行Binomial Test二项分布检验，
	检验样本事件发生率是否为0.4 --》事件发生以gwart数据中，排序小的为发生 指定α=0.05*/

    exact binomial;
    /*执行精确检验*/
run;
``` 

![../../Z appendix/Pasted image 20230217155547.png](/img/user/Z%20appendix/Pasted%20image%2020230217155547.png)  

![../../Z appendix/Pasted image 20230216173417.png](/img/user/Z%20appendix/Pasted%20image%2020230216173417.png)

## 4. 注意事项

### 4.1. 1）SAS function PROBBNML  

PROBBNML(p,n,x) 返回概率为 p 且样本大小为 n 的二项式随机变量小于或等于 x 的概率

可计算方法一的p值：
![../../Z appendix/Pasted image 20230216173437.png](/img/user/Z%20appendix/Pasted%20image%2020230216173437.png)

如下：p=PROBBNML(0.4,25,4) + (1 – PROBBNML(0.4,25,14))

### 4.2. 2）SAS function PROBNORM，
获得方法二正态近似的p值：p=2*(1 – PROBNORM(1.429) = 0.1530
其中1.429为Z值，需给定
