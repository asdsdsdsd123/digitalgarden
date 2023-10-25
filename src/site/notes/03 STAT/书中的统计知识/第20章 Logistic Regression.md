---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第20章 Logistic Regression/","dgPassFrontmatter":true}
---


## 1. Introduction

- Although all of these methods `include covariate adjustments`,： 
	- `linear regression & ANCOVA` analyze means of `numeric response measures;`  
	- `logistic regression analyzes proportions` `based on categorical responses`, most commonly binary responses (e.g., success rates, survival rates, or cure rates).  

- 若一个协变量+用药分组变量+response variable（二分类 Y N变量） ：
	- 可用这种logistic分析方法，
	- 或前面的CMH分析方法（将其中的一个协变量，作为Strata），
		- 但是CMH the ordinality of the stratification variable is ignored with the CMH test。`Sometimes binary responses within strata are correlated`, in which case the `CMH test would not be appropriate`（`如一个人记录多次response`，`resonse在subject内是独立的`，`但是所有的是关联的`。偏头痛患者服用研究药物，记录两小时内经历的偏头痛次数，以及治愈的次数。在这种情况下，response为“解决”或“未解决”的二元结果。虽然可以假设患者之间的独立性，但每个患者都可能经历几次发作。）

`logistic regression` methods `can be used` in cases that `involve ordinal categorical responses with more than two levels`

- Odds: Px/1-Px
	- 1）Odds: Px/1-Px
	- 2）OR: 增加一个单位后Px/1-Px与未增加前的比值。OR=Odds(增加后)/Odds(增加前)=e估计值
	- 3）SAS Proc Logistic通过最大似然估计法，e的估计值的次方为OR。e估计值
	- 4）计算一个单位的协变量值变化，Px的变化率：100（1-OR）

## 2. Example

### 2.1. E1: 连续协变量x2x2 

to compare relapse rates between treatment groups adjusted for prior
remission time （协变量x）。 不同于CMH，离散型协变量x2x2

![../../Z appendix/Pasted image 20230219172355.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172355.png)

#### 2.1.1. SAS Code

用proc logistic执行logistic回归，SAS程序 class语句/model语句写法与SAS类似

```sas
ods graphics;
proc logistic data=aml plot(only)=(effect oddsratio);  
		/*oddsratio与下面的oddsratio语句一起用
		Effect: obtain an effects plot*/
    class group(ref="PBO")/param=ref;   /*param=ref有的不写的话，可能结果p值不同*/
		/*CLASS statement for the nominal valued group factor
		REF=‘PBO’ option, you’re telling SAS to treat the placebo，指定安慰剂，安慰剂作为0，X+1，为用药组，反映了用药组的阳性率（p0/1-p0）对安慰剂阳性率变化的比值
		PARAM=REF option forces SAS to use the‘reference’ parameterization
		*/
    model relapse(event="YES")=group x;
		/*‘YES’ for relapse as the event*/
    oddratio group;
    oddratio x;
run;
```

![../../Z appendix/Pasted image 20230219172423.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172423.png)  

---

![../../Z appendix/Pasted image 20230219172451.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172451.png)  

---

![../../Z appendix/Pasted image 20230219172502.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172502.png)


#### 2.1.2. 不考虑协变量x的卡方检验：

```sas
proc freq data = aml; 
	tables group*relapse / chisq nocol nopercent nocum;
	title3 'Chi-Square Test Ignoring the Covariate';
run;
```

![../../Z appendix/Pasted image 20230219172528.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172528.png)

### 2.2. E2: 离散有序协变量x2x4离散有序协变量

• 分为两个treatment组：用药组，安慰剂组。
• 响应变量为胃痉挛缓解程度：（1=no response, 2=some response, 3=marked response, or 4=complete response）。
• 协变量为胃痉挛病史情况：‘no prior episodes’ [0], ‘one prior episode’ [1], or ‘more than one prior episode’[2]
• Is there any difference in response between the patients who received the experimental therapy and the untreated patients?

![../../Z appendix/Pasted image 20230219172545.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172545.png)  

![../../Z appendix/Pasted image 20230219172559.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172559.png)  

#### 2.2.1. SAS Code

```sas
proc logistic data=gi;
    class rx(ref="B") /param = ref;
    model resp(descending)=rx hist;
run;
```

![../../Z appendix/Pasted image 20230219172619.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172619.png)

`PROC LOGISTIC会根据response的level水平数，自动生成三种方程式TABLE 20.6 (i)/(ii)/(iii)`

![../../Z appendix/Pasted image 20230219172637.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172637.png)  

![../../Z appendix/Pasted image 20230219172647.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172647.png)  

### 2.3. E3: 连续协变量x2x2

binary `responses within strata are correlated`, in which case the CMH
test would not be appropriate（when a number of binary outcomes are observed in each of several patients  
`测定ED患者服药（研究药即对照药）后，一段时间发生多次outcomes(Number of Attempts)，events为（Number of Successes）`，协变量为年龄。比较不同用药组的成功率(Number of Attempts/Number of Successes)是否相同  

每个人多次events，一个人的这些outcomes作为cluster。

![../../Z appendix/Pasted image 20230219172706.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172706.png)

#### 2.3.1. SAS Code

```sas
proc logistic data = diary;  
	class trt(ref=’0’) / param = ref;  
	model succ/attpt = trt age / scale = williams;
      *events/outcomes=治疗组 协变量/选项。选项scale=，for re-scaling the covariance matrix are available*;
run;
```

![../../Z appendix/Pasted image 20230220105047.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230220105047.png)

## 3. 注意事项

### 3.1. 20.3.1 Assessing the fit of a logistic regression model 

可以加入多个协变量，模型模拟可能更好/更差，可用AIC SC评价模型模拟的合适。  
加入减少协变量，可看AIC SC的值有没有变化，评估模型合不合适

![../../Z appendix/Pasted image 20230219172722.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172722.png)

If you `include the SELECTION option in the MODEL statement`, SAS `will automatically fit a series of logistic regression models with systematic inclusion or exclusion of effects in the model`, based on their significance.  

也可在`model语句中，加入SCALE=NONE, AGGREGATE, LACKFIT。assess goodness of fit.`

### 3.2. 20.3.2 Proc Catmod也可logistic回归

除Proc Logistic外，Proc Catmod也可logistic回归，不过`Proc Catmod对于连续型协变量并不适用`。而`Proc Logistic对于连续型 离散型协变量都不限制  `  

基于广义线性模型（generalized linear models）的Proc Genmod/Proc Glimmix，model a wide array of different types of responses, such as those from a normal distribution, binomial or multinomial responses, 【可在Proc Glimmix中指定ODDSRATIO选项输出OR与95%置信区间】

![../../Z appendix/Pasted image 20230219172735.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172735.png)

### 3.3. 20.3.3 多水平的response

例子2中对于多水平的response，假设TABLE 20.6 (i)/(ii)/(iii)的β1，β2都是一样的。其中output输出部分，可以检验假设是否成立（假设是一样的）

![../../Z appendix/Pasted image 20230219172746.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172746.png)


![../../Z appendix/Pasted image 20230219172757.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172757.png)

结果>0.05，表明无差异。若结果<0.05，有显著差异，此时需注意交互作用。若无法假定β1，β2都是一样的，需要to use the LINK=GLOGIT option in PROC LOGISTIC to fit a  generalized logit model that treats the response as a nominal effect, ignoring the ordinality of the response levels

### 3.4. 20.3.4 给定协变量，预测grp的resp

Proc Logistic中，使用PREDICTED= xx选项，得出协变量是xxx的预测的概率。  

If response is multinomial, the PREDPROBS=INDIVIDUAL  
`output out = estim predicted = p_est; ①  `
`/*score选项输出score结果一样：*/score out=scores;② ` 


![../../Z appendix/Pasted image 20230219172812.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172812.png)  

![../../Z appendix/Pasted image 20230219172823.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172823.png)

![../../Z appendix/Pasted image 20230219172833.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219172833.png)  

### 3.5. 20.3.5 假设是随着协变量变化，odd变化斜率正比

默认假设是随着协变量变化，odd变化斜率正比。若该假设不成立，则需以二元方程指定协变量变化。  

assumes that the log-odds is related to incremental changes in X2 in a linear way, 
IF: log-odds of ‘one prior episode’ vs. ‘no prior episodes’ should be different from the log-odds of‘more than one prior episode’ vs. ‘one prior episode.slope cannot be assumed to be linear. In this case, a quadratic term for prior history (hist\*hist) can be included in the model

