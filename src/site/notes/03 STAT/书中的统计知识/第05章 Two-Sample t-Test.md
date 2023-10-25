---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第05章 Two-Sample t-Test/","dgPassFrontmatter":true}
---

## 1. Introduction

`判断两未知样本总体均值是否相等。`
两样本<span style="background:#fff88f">需满足正态分布，且两样本的方差相同</span>。若总体方差未知用t分布，若总体分布已知用Z分布。

## 2. Synopsis

H0表示两样本总体均值吴差异，即µ1=µ2。如果µ1 µ2二者deviation大，则表明假设不成立。检验统计量 t表示二者的deviation

The test statistic t is a function of this deviation ，t越大，倾向于拒绝原假设（µ1=µ2）。自由度为N-2（n1-1+n2-1）

![../../Z appendix/Pasted image 20230127162519.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127162519.png)

## 3. Examples

1）例1，哮喘患者随机接受口服ABC-123，安慰剂6周，6周后测定静息时的用力呼气量，研究哮喘药ABC-123是否有效。效应变量，是6周后，用力呼气量与基线的差值。有几个治疗后NA的数据剔除了。

![../../Z appendix/Pasted image 20230127162533.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127162533.png)

### 3.1. sas代码

- Proc means测定各个组内，`某变量的单样本t检验（与0比较）`

![../../Z appendix/Pasted image 20230127162737.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127162737.png)


- SAS双样本t检验 proc ttest
![../../Z appendix/Pasted image 20230127162844.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127162844.png)

![../../Z appendix/Pasted image 20230127162922.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127162922.png)

## 4. 注意事项

### 4.1. 方差齐性检查

（1）用F-test检查方差齐性，如果两样本方差齐，则二者方差的比例为1。这个比例符合F-分布（广泛永于后边的方差分析），因此F值为两样本方差比值，如前例子中， F = 0.520的平方 / 0.508的平方 = 1.04，于F分布的临界值比较，判断是否等方差。

SAS可用如下函数，计算F分布的临界F值（nU-1 upper and nL-1 lower degrees of freedom. ）。
![../../Z appendix/Pasted image 20230127163528.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127163528.png)


SAS可用如下函数，计算F分布的p值，2*(1–PROBF(1.048,10,9))

（2）`也可以用方差分析方法（如使用PROC GLM）进行两组的比较。在proc glm中，means语句加上HOVTEST option，检验是否等方差`

（3）可用ods graphics画出相关分布图，ODS Graphics in the TTEST or GLM procedures to provide a graphical vsummary display

### 4.2. 方差不齐的，用 Satterthwaite t-test （modified ）分析方法

### 4.3. 预先F检验检查方差齐性，方差齐性设置的p值，与后期t检验，设置的p值的关系。

当方差齐性设置的p值大于后期t检验设置的p值时，t检验的差异会小些

![../../Z appendix/Pasted image 20230127164407.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127164407.png)

### 4.4. 正态分布

normality can be tested by normal option in PROC UNIVARIATE in SAS。若小样本，不满足正态性，不能使用t检验。若大样本，不满足正态性，可考虑秩和检验

### 4.5. 两样本的组间分析与组内分析

如上例子中，proc means t prt进行了chg单样本的组内分析，结果显示，用药组有统计学差异，而安慰剂组，没有统计学差异。

但双样本t检验比较了两组的差异，显示二者没有统计学差异。以上，最好用双样本t检验。因为安慰剂组的respose（或正，或负）需要从用药组中分离（because the control group response must be factored out of the response from the active group ）

![../../Z appendix/Pasted image 20230127164418.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127164418.png)

### 4.6. 虽然较大的p值(> 0.10)更相信零假设的成立，这样的结论不应该没有考虑检验效能，特别是在小样本容量。

### 4.7. 单侧检验，双侧检验

在proc ttest中加上sides=选项，指定时单侧（sides=1）/双侧(sides=2)/左侧(sides=L)/右侧(sides=U)检验。