---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第16章 The Chi-Square Test卡方检验/","dgPassFrontmatter":true}
---

## 1. Introduction

（1）卡方检验：`比较两个2xr/多个gxr离散分布样本的p值是不是相等的（p1是否等于p2）`，与二项检验正态近似的方法类似。卡方检验`是一种近似检验，当正态近似有效时可以使用该检验`

（2）1）卡方检验用于列联分析（`两个分析变量都是离散的`，`2x2或gxr列联分析`）。  

在临床试验中，`gxr表示为用药组groupx响应变量response`。  
	`检验用药组和响应变量是否存在关联`，`零假设为不存在关联`。  
	`备择假设：响应变量的level的分布在不同group间不同`-->用药组和响应变量存在关联。<span style="background:#fff88f">--chi-square statistic for general association</span>

2）对于只有响应变量为有序数据的，先将有序数据排序，序次为score，检测不同组的平均score是否不同<span style="background:#fff88f">--mean scores chi-square value</span>

3）对于分组变量和响应变量都是有序数据的，可使用其他卡方统计量，检验increasing response scores与the Group scores是否有相关性<span style="background:#fff88f">--chi-square correlation statistic</span>，如不同剂量（10 20 30 40）用药组和二分类的response（二项分布，结局YN）

- 以上标黄的卡方统计量，可以在proc freq的table中加CMH选项得出：

![../../Z appendix/Pasted image 20230217163316.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217163316.png)

![../../Z appendix/Pasted image 20230217163330.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217163330.png)

- 对于二项分布的response（response只有YN的），--chi-square statistic for general association等于mean scores chi-square value【χGA2 =χMSD2】。  
	因为resonse为二项分布，其实可以视为编码为0 1的有序数据。-->r为有序，g为常规离散数据。即只要response有序，χGA2 =χMSD2。
	若是g有序（或二分类），且resp为有序，χMSD2 =χCOR2

## 2. Synopsis

先查表，得出该样本量下的卡方值，与计算的卡方统计量比较，得出是否拒绝原假设的结论

### 2.1. Example 1
例：评估某药物（用于抗生素治疗）胃肠道不良反应发生率，评估对象为某药物和红霉素。将某药物与红霉素分为两组，分别给上呼吸道感染的人治疗，评估两组的胃肠道不良反应率是否不同。

![../../Z appendix/Pasted image 20230217164331.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217164331.png)

![../../Z appendix/Pasted image 20230217164341.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217164341.png)

`该例对应的p值，可通过SAS函数计算，p = 1 – PROBCHI(5.012,1)`---->PROBCHI(卡方,自由度)
该例子`两组，自由度为2-1`，卡方如上图。计算相应p值为0.0252，p<0.05，表明两组有统计差异

#### 2.1.1. 使用SAS分析

2x2列联表

![../../Z appendix/Pasted image 20230217164524.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217164524.png)

在用法，`tables: row*column，一般row variable为group变量`
注：本例为two-sample binomial comparison，grp/resp为二分类数据的，也`要基于正态近似`。  
对于`数据样本量少，正态近似效果不好`，需要`调整卡方值/Z统计值`（Z统计值平方为卡方值）。见下图标黄的部分

![../../Z appendix/Pasted image 20230217164638.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217164638.png)

### 2.2. Example 2

gxr列联表，`tables g(行变量-group用药分组)\*r（列变量-response响应变量）`  
例：4x2，临床研究中随机分配到 `4 个剂量组`的患者的`退出率`。   
	(i) 各组之间的辍学率是否存在差异，以及 (ii) 退出率是否与剂量呈线性相关？

![../../Z appendix/Pasted image 20230217164941.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217164941.png)

![../../Z appendix/Pasted image 20230217165103.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165103.png)

#### 2.2.1. 1）其中cmh选项，  

执行以下三种卡方检验，general association/row mean scores differ/non-zero correlation

![../../Z appendix/Pasted image 20230217165032.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165032.png)

#### 2.2.2. 2）其中chisq选项， 

执行类似general association的卡方检验，值与上general association (N/(N - 1))倍数关系。而若执行Mantel-Haenszel chisquare，结果与non-zero correlation类似

![../../Z appendix/Pasted image 20230217165045.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165045.png)

与1）中CMH选项非零相关COR结果比较，p<0.05，表明趋势有统计学意义--》significant trend in response rates with increasing dosage。而GA/MSD/卡方结果表明，两组间率没有统计学意义
而这表明，虽为发现组间响应率的不同，但发现有趋势

#### 2.2.3. 3）trend选项    

进行Cochran-Armitage test。看g r有序数据的相关性
如果respose为二项分布，group为有序变量。
  trend选项指定的Cochran-Armitage test  for trend （也是基于正态近似）和COR卡方相同。 
如果g r都为有序数据，且其中有一个是二分类变量。
  trend的Cochran-Armitage test和Mantel-Haenszel卡方也可互相推理

![../../Z appendix/Pasted image 20230217165121.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165121.png)

### 2.3. Example 3

gxr列联表，2xr，response(None/Partial/Complete), group(Active Placebo)。比较response分布在组间是否有差异

![../../Z appendix/Pasted image 20230217165131.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165131.png)  

---

![../../Z appendix/Pasted image 20230217165141.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165141.png)

---

![../../Z appendix/Pasted image 20230217165153.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165153.png)

---

![../../Z appendix/Pasted image 20230217165212.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165212.png)  

---

![../../Z appendix/Pasted image 20230217165223.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217165223.png)

## 3. 注意事项

### 3.1. （1）发生率的置信区间，  

可以在sas tables语句中加上riskdiff选项（未校正）/riskdiffc选项（校正，对于n1 n2小的）计算

### 3.2. （2）默认卡方的score没有间距，  

如果reponse是“改善程度”，level为“无”、“一些”、“显著”和“治愈”，那么“显著”和“治愈”之间的相对差异在临床上可能比“无”和“一些”之间的差异更重要。  

可在proc freq中指定scores=xxx选型，指定有间距的score-->modified ridit scores在临床数据中常见SCORES=MODRIDIT 

### 3.3. （3）精确检验。

当没有cell频数为0，当20%的频数大于5。否则，若样本量很少，用<span style="background:#fff88f">精确检验</span>  
可在proc freq的exact statement中指定`FISHER`, PCHI, LRCHI, or MHCHI option。
分别对应于：
	`generalized Fisher's exact test`/
	exact Pearson chi-square(general association)/
	 likelihood ratio exact test（ Mantel-Haenszel exact test）/
	 Mantel-Haenszel exact test（ Mantel-Haenszel exact test）

（4）会议中提到的分层卡方检验
分层变量用药组发生率比较：

![../../Z appendix/Pasted image 20230217172517.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230217172517.png)

