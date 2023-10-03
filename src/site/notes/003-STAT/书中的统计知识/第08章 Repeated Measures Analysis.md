---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第08章 Repeated Measures Analysis/","dgPassFrontmatter":true}
---


## 1. Introduction

Repeated Measures Analysis，指从一个experimental unit上获得了多个测量数据（如对同一病人进行一段时间的连续评估），这些数据是非独立的（independent）,correlation structure。如大多数临床研究，要求门诊患者在试验期间多次返回诊所，每次都进行反应测量 （如多个访视测量生命体征/肿瘤大小）。这些数据一般是纵向的，可以描述response profile随时间的变化。



## 2. Synopsis 

Repeated Measures Analysis：通过`执行"univariate" method that is based on the same ANOVA （单变量方差分析方法）-univariate ANOVA` ，或许还需要`加上"multivariate" method。多基于广义线性模型，这个模型多用MIXED, GLIMMIX, and GENMOD procedures in SAS，如数据有大量缺失，协方差结构多样化。`

`如n个病人，按照给药（治疗药，安慰剂）group分组，在多个访视/times中，测定了response。`

Consider the Group and Time effects as two factors in an ANOVA. `Group effect as a fixed treatment effect and the Time effect as a blocking factor`。与two-way anova不同的是，`two-way anova要求数据独立，但多次测量的anova，同一病人不同times不独立，但不同病人的数据独立`

除此之外，需假设`Patient (within each Group) as another effect`，并设定该部分的协方差或相关结构（ the covariance or correlation structure of the repeated measurements within Patients ），

变异可能出现在一下几个部分：among groups, among patients within groups, and among the different measurement times ---》对应于effects（a Group effect, a Patient (withinGroup) effect, and a Time effect ）。还要研究Group-byTime interaction. 

Two-way anova，`需要样本正态分布，group间方差齐性`，除此之外univariate ANOVA，要求每对重复测量具有相同的相关性（requires that each pair of repeated measures has the same correlation ）--compound symmetry 
。

![../../Z appendix/Pasted image 20230207230844.png](/img/user/Z%20appendix/Pasted%20image%2020230207230844.png)

Variation from patient-to-patient is one type of random error 

## 3. Examples 

### 3.1. （1）Example 1

评估一种新疫苗对因关节炎爆发而引起的不适的效果。4名患者被随机分配接受活性疫苗，4名患者接受安慰剂。患者被要求3个月中，每月返回诊所
评估他们在前一个月的日常家务的舒适水平，使用0(无不适)至10(最大不适)的量表。入选标准，要求患者在接种疫苗前一个月的评分至少为8分

![../../Z appendix/Pasted image 20230207231034.png](/img/user/Z%20appendix/Pasted%20image%2020230207231034.png)

![../../Z appendix/Pasted image 20230207231052.png](/img/user/Z%20appendix/Pasted%20image%2020230207231052.png)

![../../Z appendix/Pasted image 20230207231108.png](/img/user/Z%20appendix/Pasted%20image%2020230207231108.png)

![../../Z appendix/Pasted image 20230207231121.png](/img/user/Z%20appendix/Pasted%20image%2020230207231121.png)


response profile，the vector of longitudinal means over time---(纵向（时间）的均值)  is usually called the mean response profile.

如图，横坐标是时间（time），纵坐标是response

![../../Z appendix/Pasted image 20230207231230.png](/img/user/Z%20appendix/Pasted%20image%2020230207231230.png)


（1）假设检验及结果：

The null hypothesis of similar response profiles over time for each of the vaccine
groups is tested by the Vaccine-by-Visit interaction 。The null hypothesis can be expressed as the simultaneous equality of Treatment Group differences at each time point 。
H0：比较每个时间点的两用药组疗效是否一致（可以按照图片8.1理解，如图第一行的俩图。所以分子为Vaccine-by-Visit），验证交互作用。H0存在不存在交互作用。

![../../Z appendix/Pasted image 20230207231419.png](/img/user/Z%20appendix/Pasted%20image%2020230207231419.png)

（2）两factor/effects的交互：
图片8.1也显示了交互作用，表明 Vaccine Group differences depend on the time point considered。这里需要group-time的单因素方差分析（如one-way anova，及第六章的linear contrasts），to compare differences between vaccine groups in the changes from successive time points 

#### 3.1.1. PROC GLM检验p及两factor/effect交互

![../../Z appendix/Pasted image 20230207231519.png](/img/user/Z%20appendix/Pasted%20image%2020230207231519.png)

![../../Z appendix/Pasted image 20230207231606.png](/img/user/Z%20appendix/Pasted%20image%2020230207231606.png)

#### 3.1.2. PROC MIXED检验p及两factor/effect交互（更好）

多次测量分析方法，假设要求每人的多次测定间相关性相同。但time时点多了，不一定满足相关性相同的条件。
When using PROC MIXED in the SAS analysis, you can do away with all assumptions by designating an ‘unstructured’ covariance type,  also allows for heterogeneous variances across the time points.

![../../Z appendix/Pasted image 20230207231636.png](/img/user/Z%20appendix/Pasted%20image%2020230207231636.png)

![../../Z appendix/Pasted image 20230207231653.png](/img/user/Z%20appendix/Pasted%20image%2020230207231653.png)

![../../Z appendix/Pasted image 20230207231710.png](/img/user/Z%20appendix/Pasted%20image%2020230207231710.png)


### 3.2. （2）Example 2

`每个cell 不对称分布-公式不适用，例1公式只balanced layout same number of patients per group` 
减轻间歇性跛行症状，分为治疗组和安慰剂组，实行4个月的试验（visit=baseline 4 8 followup），加入了stratification blocking effect （sex），response为跑步机跑步距离（开始至因跛行停止的跑步距离）

以往的分析：each visit的用药组/安慰剂组、比较；each visit的用药组chg与安慰剂组chg的比较--可采用two-sample t-test/one,two-way anova，如以上章节所示

本例主要关注，changes over time ，尤其是Treatment-by-Visit interaction ，比较两用药组间的response profile

![../../Z appendix/Pasted image 20230207232112.png](/img/user/Z%20appendix/Pasted%20image%2020230207232112.png)

![../../Z appendix/Pasted image 20230207232132.png](/img/user/Z%20appendix/Pasted%20image%2020230207232132.png)

因为sample size imbalance ，因此不能像8.1那样按公式计算SS，且要用Type III SS。
因为没有缺失数据，PROC GLM的多因素分析（Two-Way ANOVA）分析也能产生很好的结果。
然而to allow greater latitude in assumption correlations in measurements across visits, 使用PROC MIXED更好

#### 3.2.1. PROC MIXED检验

![../../Z appendix/Pasted image 20230207232356.png](/img/user/Z%20appendix/Pasted%20image%2020230207232356.png)

![../../Z appendix/Pasted image 20230207232411.png](/img/user/Z%20appendix/Pasted%20image%2020230207232411.png)

![../../Z appendix/Pasted image 20230207232422.png](/img/user/Z%20appendix/Pasted%20image%2020230207232422.png)

![../../Z appendix/Pasted image 20230207232436.png](/img/user/Z%20appendix/Pasted%20image%2020230207232436.png)

![../../Z appendix/Pasted image 20230207232455.png](/img/user/Z%20appendix/Pasted%20image%2020230207232455.png)


------------------------------------`repeated语句后加入type=cs选项，指定方差结构`--------------------
----------------------------加入lsmeasn两两比较，指定选项只比较各个Month与治疗组的，而治疗组的不比较-----------
------------------------------------加入estimate指定只比较用药组/安慰剂组的baseline month4---------

![../../Z appendix/Pasted image 20230207232617.png](/img/user/Z%20appendix/Pasted%20image%2020230207232617.png)

![../../Z appendix/Pasted image 20230207232634.png](/img/user/Z%20appendix/Pasted%20image%2020230207232634.png)

![../../Z appendix/Pasted image 20230207232650.png](/img/user/Z%20appendix/Pasted%20image%2020230207232650.png)

![../../Z appendix/Pasted image 20230207232706.png](/img/user/Z%20appendix/Pasted%20image%2020230207232706.png)

### 3.3. （3）Example 3

`有mssing data`

治疗老年痴呆，用药组低剂量L 高剂量 H安慰剂组P，resonse为评分指标（值越高证明病情越严重），病情本身随时间延长而加重，证明用药组（高低剂量）比安慰剂组病情进程缓慢。

#### 3.3.1. PROC MIXED 

![../../Z appendix/Pasted image 20230207233024.png](/img/user/Z%20appendix/Pasted%20image%2020230207233024.png)

![../../Z appendix/Pasted image 20230207233127.png](/img/user/Z%20appendix/Pasted%20image%2020230207233127.png)

`通过PROC GLIMMIX in SAS with the COVTEST statement 寻找合适的协方差结构：`

  ![../../Z appendix/Pasted image 20230207233215.png](/img/user/Z%20appendix/Pasted%20image%2020230207233215.png)

`Ods select，选择输出哪些output：`
  ![../../Z appendix/Pasted image 20230207233319.png](/img/user/Z%20appendix/Pasted%20image%2020230207233319.png)


## 4. 注意事项

### 4.1. 用proc genmod：more robust than proc glm/mixed，

However, PROC GENMOD cannot handle random effects.
In the analysis above, GENMOD treats patient as a fixed effect, whereas PROC MIXED treats patient as a random effect in Output 8.5

![../../Z appendix/Pasted image 20230207233419.png](/img/user/Z%20appendix/Pasted%20image%2020230207233419.png)


### 4.2. 当缺失数据非常多时，repeated measures analysis ，不推荐使用

（1）对于单因素重复测量：result in complex hypotheses and interpretation difficulties 
‘univariate’ approach to repeated measures analysis should be avoided when the data set is plagued with missing data (just a few missing values in a large study should not present interpretation difficulties). 
（2）对于多因素重复测量：decreasing the test’s power 
（3）PROC MIXED对缺失数据有一定抗性，但需要缺失数据满足MAR假设
MAR假设：缺失数据等价于从sample完全随机抽样产生如例8.3中，`只有5%的缺失数据`，且`缺失数据与用药无关`，`与暂停试验无关`，可视为满足MAR假设。<mark style="background: #FFB8EBA6;">但是临床上很多缺失数据是因为，无效或副作用而早期停药产生的，不满足MAR假设</mark>。

需对缺失值填充LOCF，填充上次的value；
相邻值均值，用某行某列的均值，回归技术等（也很大程度依赖MAR假设）

### 4.3. 没有缺失数据的单因素 proc glm的F-Test结果，等价于proc mixed  using the compound symmetric covariance structure (TYPE=CS), 
 
### 4.4. You can also use PROC GLIMMIX in SAS with the COVTEST statement to test covariance structures 

可在proc mixed model语句中加选项，调整自由度，控制偏倚--DDFM=KR 或 DDFM=SAT 或用DDF= XX自行指定自由度

PROC MIXED performs analyses using estimates of the covariance parameters, these can differ from one time point to another. The methods can also be improved by using adjusted degrees of freedom, especially the degrees of freedom in the denominator of the F-tests, which are affected by the covariance structure 

### 4.5. 对于非正态分布数据 重复测量的多因素analysis

Analysis includes additional factors or covariates, more sophisticated modeling using Weighted Least Squares (WLS)--PROC CATMOD or Generalized Estimating  Equations (GEE) --PROC GENMOD 
PROC GLIMMIX 用途更广，可用于normal/non-normal/random effect/fixed effect/independent/corrected models





