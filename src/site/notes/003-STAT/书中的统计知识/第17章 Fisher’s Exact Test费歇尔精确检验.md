---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第17章 Fisher’s Exact Test费歇尔精确检验/","dgPassFrontmatter":true}
---

## 1. Introduction

Fisher's Exact Test，是一种卡方检验，
	计算`极端情况下（cell频数小，发生率过大或过小-->不满足正态近似）`的实际`准确的p值`。--如，单独cell的count<5,所有<40?：小样本
	
## 2. Examples 

ARA具有降低心脏病手术的副作用，grp为ARA及安慰剂，response为副作用是否发生。检验ARA的副作用发生率是否小于安慰剂。
考虑检验的多个极端情况，并把多个情况的p值相加，获得该检验的p值

![../../Z appendix/Pasted image 20230217172753.png](/img/user/Z%20appendix/Pasted%20image%2020230217172753.png)

---

### 2.1. SAS Code

![../../Z appendix/Pasted image 20230217172806.png](/img/user/Z%20appendix/Pasted%20image%2020230217172806.png)

---

![../../Z appendix/Pasted image 20230217172829.png](/img/user/Z%20appendix/Pasted%20image%2020230217172829.png)

---

![../../Z appendix/Pasted image 20230217172845.png](/img/user/Z%20appendix/Pasted%20image%2020230217172845.png)

---

![../../Z appendix/Pasted image 20230217172900.png](/img/user/Z%20appendix/Pasted%20image%2020230217172900.png)


## 3. 注意事项

### 3.1. （1）应用情况

对于cell频数多的，也可采用Fisher’s Exact Test，但是需要更多的计算资源。可使用Monte Carlo techniques with PROC FREQ 使运算更有效率

### 3.2. （2）双尾检验过程：

![../../Z appendix/Pasted image 20230217173210.png](/img/user/Z%20appendix/Pasted%20image%2020230217173210.png)  

![../../Z appendix/Pasted image 20230217173223.png](/img/user/Z%20appendix/Pasted%20image%2020230217173223.png)  

### 3.3. （3）fiser可检验推广到2xr, gx2, gxr

与卡方test章节讲到的一样，可以将fiser检验推广到2xr, gx2, gxr。如前：
若样本量很少，用精确检验。

可在proc freq的exact statement中指定FISHER, PCHI, LRCHI, or MHCHI option。
分别对应于generalized Fisher's exact test/
exact Pearson chi-square(general association)/
 likelihood ratio exact test（ Mantel-Haenszel exact test）/
 Mantel-Haenszel exact test（ Mantel-Haenszel exact test）

