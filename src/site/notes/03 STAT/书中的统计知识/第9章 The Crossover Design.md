---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第9章 The Crossover Design/","dgPassFrontmatter":true}
---


## 1. Introduction

`交叉试验设计，同一个受试者前后分为不同用药组。`  
在交叉研究中，治疗的反应是相关的，因为它们是在同一个病人身上测量的。与8章重复测量类似。  

crossover is a special case of a repeated measures design 。  
Procedures for treatment comparisons `must consider this correlation` when analyzing data from a crossover design.   
`可研究以下effects： period effects, sequence effects, and carryover effects`   

normally used in studies with short treatment periods, most frequently in pre-clinical and early-phase clinical studies 

## 2. Synopsis 

见书中

## 3. Examples 

### 3.1. （1）Example 1:

**研究用药组和安慰剂组，出汗效果。y--至满头大汗的时间**

![../../Z appendix/Pasted image 20230211170349.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211170349.png)

#### 3.1.1. SAS Code

![../../Z appendix/Pasted image 20230211170443.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211170443.png)  

![../../Z appendix/Pasted image 20230211170523.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211170523.png)

### 3.2. （2）Example 2:

测药代AUC，一个人pat给四种给药组，dose=A/B/C/D，  
各受试者分为四个给药路线，seqgrp ABDC/BCAD/CDBA/DACB。
per：代表给药次序，1，2，3，4  
Respnse variable: auc, 一定时间后AUC曲线下面积  
co：交叉设计中，该受试者上次的给药组  

![../../Z appendix/Pasted image 20230211170659.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211170659.png)

## 4. 注意事项

### 4.1. 某些情况下，x\*xx与xxx pvalue相等

In a two-period crossover design `under the assumption of no carryover effect` (such as in Example 9.1)   
Differences between Sequence Groups in the Treatment comparisons are indistinguishable（难以分辨的） from Period differences   
因为难以分辨，因此程序中，都是pd(用药次序1 2 3) pvalue与trt\*seq（用药路线 AB BA） pvalue相等的。  
类似的trt pvalue与pd\*seqgrp pvalue相等的  
后边这样交叉项的写，与8章重复测量类似

### 4.2. Example 9.1 two-period crossover design (A-B/B-A) ，没有carryover effect 可以用ttest

![../../Z appendix/Pasted image 20230211171121.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211171121.png)  
![../../Z appendix/Pasted image 20230211171138.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211171138.png)   

### 4.3. balanced residuals 

![../../Z appendix/Pasted image 20230211171442.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211171442.png)

例9.2包括一种carryover effect(co)，该effect被认为对所有剂量都是相同的。  
如果认为每个剂量水平的carryover effect(co)可能不同，可以include several carryover or residual effects   

### 4.4. crossover designs can also be performed with non-normal responses, which include categorical and rank data 


