---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第14章 Kruskal-Wallis Test/","dgPassFrontmatter":true}
---


`治疗组多分类，非正态分布非方差齐性或有序数据`

## 1. Introduction 

也是一种非参数检验，`相当于one-way anova，都是用于多个group（group变量有两个以上level）的组间比较。`

a non-parametric analogue of the one-way ANOVA (Chapter 6)。数据不需要满足正态性，是13章Wilcoxon rank-sum test的扩展。数据是基于秩次的，`The output from PROC NPAR1WAY also gives the results of the analysis using the Kruskal-Wallis chi-square approximation`

## 2. Examples 

两种剂量的药物+安慰剂`三个治疗组`，对32例患者进行为期4周的治疗，`response variable为研究结束时牛皮癣皮损减轻的程度`

![../../Z appendix/Pasted image 20230211225025.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225025.png)

Response variable`数值有次序`，与病情的好坏有关

![../../Z appendix/Pasted image 20230211225035.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225035.png)

![../../Z appendix/Pasted image 20230211225132.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225132.png)


注意：`wilcoxon符号秩检验（proc univariate）/wilcoxon秩和检验(proc npar1way)/KW检验(proc npar1way)`基本语法`与前(proc glm/mixed)不同`，  
相同点，都用class语句指定group variable；  
不同点，前边用model指定模型，后边用var指定resonse variable

## 3. 注意事项 

1）对于小样本（n<5）的非参KW检验不适用。在SAS中可using the EXACT statement in PROC NPAR1WAY，当tests involving very small sample sizes or many ties.即进行费歇尔-卡方精确检验

2）PROC NPAR1WAY 加选项anova，在KW卡方检验下输出单因素方差分析。比较二者结果，如果二者相差太多，则可通过proc univariate normal检验正态性，看哪个更合适


![../../Z appendix/Pasted image 20230211225151.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225151.png)  

![../../Z appendix/Pasted image 20230211225203.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225203.png)  

3）可结合rank+单因素方差分析，达到KW检验类似效果
不过需要前提：在不同组别的等级中，显示出类似的离散性（类似方差齐性）。可通过ods graphics图判断。
若KW检验前提不满足时，rank+单因素方差分析是很好的替代方法

![../../Z appendix/Pasted image 20230211225224.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225224.png)  

![../../Z appendix/Pasted image 20230211225235.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225235.png)  

4）若分组变量是order的，如本例，检验 whether increasing response is related to larger doses，可以用Jonckheere-Terpstra test 

![../../Z appendix/Pasted image 20230211225246.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225246.png)  

![../../Z appendix/Pasted image 20230211225305.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211225305.png)


5）rank+多因素方差分析，先按照grp block排序，得出proc rank的秩次。后边基于秩次进行P多因素ANOVA分析---》等同于Friedman's test（nonparametric analog of the two-way ANOVA，假设每个cell的观测大于1）

