---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第22章 The Cox Proportional Hazards Model生存分析带协变量/","dgPassFrontmatter":true}
---

## 1. Introduction

Cox proportional hazards model (sometimes referred to as ‘Cox regression’) is `used to analyze event or ‘survival’ times`, which `adjusts for censoring` included `in numeric covariates` (may be time-dependent) `and Categorical factors.`

Cox proportional hazards model is the way in which the hazard changes over time.
Cox比例风险模型：是风险随时间变化的模型    

`Hazard-->1/【事件发生的时间，即生存时间】`，
	如：event is expected to occur in 6 months, the hazard is 1/6 (‘events per month’)

hazard of some events might be likely to increase with the passage of time，如冠状动脉搭桥后，再梗死的风险在刚做完手术最高，随时间增长，后边逐渐降低。也有hazard的其他变化情况

`Cox proportional hazards model adopts the more reasonable assumption `that the `event hazard rate can change over time`, but that the ratio of event hazards between two individuals is constant

原理与logistic回归类似，把用药组grp与协变量covariate都当成一样的下图中的x1 x2，β1 β2表示对应x1 x2的影响

![../../Z appendix/Pasted image 20230219184040.png](/img/user/Z%20appendix/Pasted%20image%2020230219184040.png)

<span style="background:#fff88f">--------------------------对SAS输出结果中危险比的理解，------------------------------</span>
![../../Z appendix/Pasted image 20230219184053.png](/img/user/Z%20appendix/Pasted%20image%2020230219184053.png)

![../../Z appendix/Pasted image 20230219184101.png](/img/user/Z%20appendix/Pasted%20image%2020230219184101.png)  

![../../Z appendix/Pasted image 20230219184109.png](/img/user/Z%20appendix/Pasted%20image%2020230219184109.png)  

## 2. Examples

### 2.1. E1: 之前病发次数x二分类trtx生存时间

一个协变量，二分类的treatment group，给定生存时间（从开始到event发生时的时间） 

协变量：试验研究前疾病发作次数。分成两个用药组，比较不同组复发的时间分布是否相同。 
Does covariate X(试验研究前疾病发作次数) have any impact on the comparison of HSV-2 recurrence times between treatment groups

![../../Z appendix/Pasted image 20230219184124.png](/img/user/Z%20appendix/Pasted%20image%2020230219184124.png)  

```sas
proc phreg data=hsv;
	class vac(ref="PBO")/param=ref;
	model wks*cens(1)=vac x /ties=exact;
	/*与proc lifetest类似，只是语句不同
	；proc lifetest核心语句：time wks*cens(1)*/
run;
```

![../../Z appendix/Pasted image 20230219184150.png](/img/user/Z%20appendix/Pasted%20image%2020230219184150.png)

### 2.2. E2: 多个多种类型协变量x二分类trtx生存时间
（2）多个协变量，二分类的treatment group，给定生存时间（从开始到event发生时的时间）

![../../Z appendix/Pasted image 20230219184207.png](/img/user/Z%20appendix/Pasted%20image%2020230219184207.png)
其中inftim为 time-dependent，随着试验的进行，可能出现/不出现/或早/或晚感染并发症，且即前言提到的(hazard of some events might be likely to increase/decrease with the passage of time)-->随时间变化，出现并发症的风险会增大/减小。
这里只取在生存时间前的并发症，这种情况才认为是并发症对event(出血消除)有关:

```
if inftim gt rsptim or inftim = . then infctn = 0;      
        else infctn = 1; 
```

```sas
proc phreg data=vitclear2;
	class trt(ref="SAL") center(ref='C')/param=ref;
		/*Used reference parameterization,reference levels are ‘SAL’
			for treatment group and ‘C’ for study center */
	model rsptim*cens(1)=trt center age dens infctn/ties=exact;
	
	if inftim gt rsptim or inftim = . then infctn = 0;   
        else infctn = 1; 
	
	/*infctn 的生成，注意位置，写在前面data step，产生结果略有不同*/   
	
run;
```

![../../Z appendix/Pasted image 20230219184258.png](/img/user/Z%20appendix/Pasted%20image%2020230219184258.png) 

## 3. 注意事项

### 3.1. determines whether any of the covariates are significant after adjusting for all the other covariates

PROC PHREG `prints the results of a global test` that `determines whether any of the covariates` included in the model `are significant after adjusting for all the other covariates. `

### 3.2. 检验covariate-by-time ‘interaction’(Test the hazard changes over time )

‘Non-proportional hazards’ implies an increasing or decreasing hazard ratio over time。 

22.2例中，  检验covariate-by-time ‘interaction’，在model语句后，指定：tdens = dens\*rsptim;【不过建议用tdens = dens*(log(rsptim ) – 2.85);--->2.85 is the mean of the ln(RSPTIM)over all observations】

`A significant test for the parameter that is associated with TDENS` would `indicate that the hazard changes over time`

You can also use ODS plots generated with the ASSESS statement in PROC PHREG to visually check the proportional hazards assumption for numeric covariates that are not time dependant 

<font color="#ff0000">若经检验，结果不满足assumption，可对covariate value值拆分，再检验。By using the STRATA statement. </font>If the covariate has continuous numeric values, the strata can be defined by numeric intervals in the STRATA statement

对于确定每个group值，have different hazard functions,可将其列在strata语句，而非model语句中：if you believe the different study centers in Example 22.2 might have different hazard functions, you would include center in the STRATA statement instead of the MODEL statement.

### 3.3. when ties occur in event times。死亡时间相同的情况多时？

An adjustment might be necessary when ties occur in event times.可以有以下几种选项，ties=breslow/efron/exact/discrete

![../../Z appendix/Pasted image 20230219184315.png](/img/user/Z%20appendix/Pasted%20image%2020230219184315.png)

`默认选项是breslow，小样本时用exact较多，样本多时用efron较多`
