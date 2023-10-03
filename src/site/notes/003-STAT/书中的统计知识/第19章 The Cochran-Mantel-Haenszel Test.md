---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第19章 The Cochran-Mantel-Haenszel Test/","dgPassFrontmatter":true}
---

## 1. Introduction

1.compare `two binomial proportions from independent populations based on stratified samples  
---与卡方的区别：`卡方不指定block分层因素`  

2.`stratified  represent patient subgroups`, such as study centers,
gender, age group, or disease severity, and acts similar to the blocking factor in a two-way ANOVA

3.Cochran-Mantel-Haenszel test `obtains an overall comparison` of response rates `adjusted for the stratification variable`  

4.often used in the comparison of response rates between two treatment groups in a multi-center study using the study centers as strata.

## 2. Examples

### 2.1. E1: 4(cntr)x2(trt) x2(resp)

4个研究中心（cntr），2x2（treatment\*response），2个治疗组，响应也两水平。

如下数据：

![../../Z appendix/Pasted image 20230217174709.png](/img/user/Z%20appendix/Pasted%20image%2020230217174709.png)

#### 2.1.1. SAS code-cntr stra

```sas
proc freq data=ulcr;
	tables cntr*trt*resp/cmh nopercent nocol ;
	weight frq;
	title "with stra";
run;
```

![../../Z appendix/Pasted image 20230217175702.png](/img/user/Z%20appendix/Pasted%20image%2020230217175702.png)

#### 2.1.2. SAS code-without stra

```sas
proc freq data=ulcr;
	tables trt*resp/cmh nopercent nocol ;  /*去掉cntr分层变量*/
	weight frq;
	title "without stra";
run;
```

![../../Z appendix/Pasted image 20230217175721.png](/img/user/Z%20appendix/Pasted%20image%2020230217175721.png)

结果与卡方检验类似（不完全一样）：
```sas
proc freq data=ulcr;
	tables trt*resp/chisq nocol nopercent;
	weight frq;
	title "chisq";
run;
```

![../../Z appendix/Pasted image 20230217175736.png](/img/user/Z%20appendix/Pasted%20image%2020230217175736.png)

### 2.2. E2: 2(hxdep)x3(trt)x 4(resp, 有序)

按照是否抑郁分层，3x4（treatment\*response），3个治疗组，响应是有序的，四水平。 
如下数据：

![../../Z appendix/Pasted image 20230217175749.png](/img/user/Z%20appendix/Pasted%20image%2020230217175749.png)

#### 2.2.1. SAS code-cntr stra

```sas
proc freq data=fmpain;
	tables hxdep*trt*imprv/cmh nopercent nocol scores=modridit;
	/*因为是有序的，因此定义scores=modridit*/
	weight cnt;
run;
```

注意：选择Row Mean Scores Differ ，因为response为有序的

![../../Z appendix/Pasted image 20230217175956.png](/img/user/Z%20appendix/Pasted%20image%2020230217175956.png)

#### 2.2.2. 其他方法

```sas
proc catmod data=fmpain;
	weight cnt;
	response means;
	model imprv=hxdep trt;  
	/*可以指定交互项： imprv=hxdep trt trt*hxdep 
	strata与group交互含义可见第一张图最下面注释*/
run;
```

![../../Z appendix/Pasted image 20230217180044.png](/img/user/Z%20appendix/Pasted%20image%2020230217180044.png)




