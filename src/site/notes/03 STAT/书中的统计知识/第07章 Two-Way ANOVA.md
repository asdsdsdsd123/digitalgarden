---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第07章 Two-Way ANOVA/","dgPassFrontmatter":true}
---


## 1. Introduction

- 与协方差分析类似，不过这个除grp外的factor为离散型；而协方差分析除grp的factor为连续型
- `在One-Way anova基础上（一个response，一个factor如grp），`
`加上一个/或多个自变量（factor）--该自变量的变异已知，可从总变异中扣除这部分变异，即得单纯的grp对response的影响`

> `加上的主因素之外的factor->是对试验有影响的，排除这部分影响，结果更准确（降噪）：`
> - 1.例如，比较两组体重差异。因为两组男女比例不同，一组男性偏多，则因为男女体重差异较大，组内变异（误差变异）较大，得出F=组间/组内。若组间有差异，但由于组内误差大，F偏小，更易得出没有差异的结论。此时组内误差也包含了性别对体重的影响（随着性别不同，体重不同）（gender的变异）
> - 2.将gender也作为变异的一种，计算该factor的SS，MS，F。并从整体中扣除gender的这部分变异。In the two-way ANOVA (Chapter 7), you can test whether Gender is a significant factor, and remove its variation from the MSE to get a more precise test for the Treatment effect. 

- <span style="background:#affad1">前提，要求测量值所有满足正态分布，用药group组间方差齐性</span>

  ![../../Z appendix/Pasted image 20230206165057.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165057.png)
## 2. Synopsis

![../../Z appendix/Pasted image 20230206165116.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165116.png)

![../../Z appendix/Pasted image 20230206165127.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165127.png)


![../../Z appendix/Pasted image 20230206165140.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165140.png)
## 3. examples

### 3.1. 例1

（1）某药物治疗化疗后的贫血，response为baseline 及给药后的血红蛋白含量，肿瘤分型作为另一factor，判断该药对治疗化疗后贫血有没有作用

![../../Z appendix/Pasted image 20230206165201.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165201.png)

![../../Z appendix/Pasted image 20230206165210.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165210.png)

![../../Z appendix/Pasted image 20230206165220.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165220.png)
![../../Z appendix/Pasted image 20230206165231.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165231.png)

![../../Z appendix/Pasted image 20230206165245.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165245.png)

![../../Z appendix/Pasted image 20230206165302.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206165302.png)

SAS可用FINV(1-α,U,L) 函数计算临界F值，U为group组（primary effect）的水平数-1，L为总数（N）-cell数（如上例子中6个cell） 

#### 3.1.1. SAS Two-way anova (proc glm)

![../../Z appendix/Pasted image 20230206170024.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170024.png)

![../../Z appendix/Pasted image 20230206170037.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170037.png)
Model hgbch=trt type trt\*type;

![../../Z appendix/Pasted image 20230206170305.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170305.png)

![../../Z appendix/Pasted image 20230206170330.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170330.png)

Lsmeans type / pdiff stderr t;  (后边加上Lines之后，只有一个图输出，可能与SAS改版有关？)--还有好像已经默认html输出pdiff了，（可能要是输出数据集的话还需要加pdiff的选项）

![../../Z appendix/Pasted image 20230206170440.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170440.png)

![../../Z appendix/Pasted image 20230206170452.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170452.png)

### 3.2. 例2

某药物可提高记忆力，response为y回忆起的卡片数，试验中心作为另一factor，判断该药对提升记忆力有没有作用

#### 3.2.1. SAS Two-way anova (proc glm)

1）按照例子1中步骤先来看初步结果：


![../../Z appendix/Pasted image 20230206170701.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170701.png)

2）以上图显示，factor间有相互作用--->test for dose group differences within each center. 

lsmeans dose\*center / stderr pdiff; 

![../../Z appendix/Pasted image 20230206170903.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206170903.png)

#### 3.2.2. fixed effect & random effect

3）anova中有两种factor (effects)，fixed effect 和random effect。二者区别在于其level是否确定。

fixed effect ：All the effects in the examples in this chapter so far have been considered fixed, that is, having pre-specified levels 

`random effect：A random effect is one whose levels are randomly selected from a large population of levels .`

如例7.2虽然指fixed effect，研究中心经常被认为是一个random effect，因为研究中心通常代表了大量可用于开展研究的中心的一个样本，特别是当研究包括许多中心时。可调整glm中的语句，指定center作为random effects

`random center dose*center;  （写在Model语句后，其后面再写lsmeans dose\*center / stderr pdiff; ）`

```sas
proc glm data=memry;
	class dose center;
	model y=dose center dose*center/ss3;
	random center dose*center/ss3;
	lsmeans dose/pdiff stderr;
run;

proc mixed data=memry;
	class dose center;
	model y=dose;
		random center dose*center;
		lsmeans dose/diff;
run;
```

#### 3.2.3. test语句，执行指定F检验

4）执行指定的test，The TEST statement instructs SAS to form the F-test as the ratio of mean square for ‘h’ to the mean square for ‘e’, where h is the effect of interest (dose) and e is the appropriate ‘error’ term (dose\*center), as follows: 

test h=dose e=dose\*center; 

#### 3.2.4. SAS Two-way anova (proc mixed)

5）使用proc mixed执行two-way anova，

![../../Z appendix/Pasted image 20230206171540.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206171540.png)

将center作为random efftct，产生的report及分析 

![../../Z appendix/Pasted image 20230206172258.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206172258.png)

![../../Z appendix/Pasted image 20230206171616.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206171616.png)

 `proc mixed与proc glm的不同：`1>输出两两比较：Lsmeans的pdiff+stderr=diff；2>固定效应，随机效应指定不同
3>proc glm的means语句输出各组均值，标准差。Proc mixed应该是自动输出？】

这部分没有考虑到相互作用，因为center dose\*center作为了random effect。
This mixed model analysis  enables you to make inferences about the Dose effect that applies to the entire population （而不是某几个level）--所以把center作为random effect，而非某几个水平的fixed effect，消除了因某几个level分析可能会带来的偏倚。

![../../Z appendix/Pasted image 20230206171807.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206171807.png)

## 4. 注意事项

### 4.1. 在例7.1中的two-way anova回顾：

将Cancer Type作为一个ANOVA的factor，是为了提高治疗比较的精度，通过identify不同Cancer Type之间的变异，并将它们从误差变异(MSE)的估计中去除。最后使MSE变小（从之前总的MSE-Cancer type独有的MSE），F检验的分母变小，F值更大，倾向于拒绝接受原假设。

> Procedures in SAS, like MIXED, GENMOD, GLIMMIX, and NLMIXED, have wide application in statistical modeling 

### 4.2. two-way anova的不均匀设计的方差和分析方法，

选择type I/II/III/IV SS的选择

由于two-way anova的不均匀设计（每个cell人数不同）等。Type III corresponds to the method of weighted squares of means and is often the method of choice for the analysis of clinical data 

### 4.3. multi-center test

因为各center条件不同，易产生center的误差，而使MSE值增大。identify all important sources of variation that can be factored out of the experimental error to yield a more precise test for treatment effect. 

### 4.4. two-way anova(with a fixed block effect)

用two-way anova，加入fixed block efect/factor，比较treatment对response的影响，精度比仅比较treatment对respomnse的One-Way anova（忽略block effect）结果差。the MSE might increase appreciably if among-block variation is small and the number of levels of the blocking factor is large 

### 4.5. 两两比较 （类似means-多重比较Adjust，有显著交互作用等）

Proc glm/proc mixed，`通常用lsmeans xxx/pdiff(proc glm中用pdiff，proc mixed中用diff)，使用t-test方法进行两两比较。`

Multiple comparison procedures controlling for the overall significance level should be used when the number of comparisons becomes large。多重比较会使总计检验水平α变大，因此需要调整阿尔法。

`可在lsmeans 语句加adjust选项，进行调整。`You can use the ADJUST= option of the LSMEANS statement to get adjusted p-values for multiple tests. 同第六章一样，`可以用CONTRAST 语句进行pairwise or custom comparisons `

`当有明显的交互作用时，可在lsmeans语句中加上slice选项`，如：lsmeans dose\*center / slice=center; 其中slice=，后边指定的是block factor

### 4.6. 不止两个factor的anova，如执行Tree-anova及其交互作用

![../../Z appendix/Pasted image 20230206172931.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230206172931.png)

only meaningful interactions or interactions that might lead to important subgroup differences should be considered when performing the final analysis.

`列出以上全部的交互作用：model score=trt|center|sex ----->`
`Model score=trt center sex trt*center trt*sex center*sex trt*center*sex;`

### 4.7. ‘fixed-effects’ model & ‘random-effects’model & mixed effect model

- ANOVA statistical model分为 fixed-effects model----固定效应模型（所有effect/factor都是fixed ），
random-effects model-----随机效应模型（所有effect/factor都是random），
mixed effect model（a mixture of fixed and random effects），

一般临床试验的dose/treatment group都是fixed effect ，所以临床试验统计模型是fixed-effects modle/mixed-effects modle

- 需预先设计好，变量作为fixed/random effect。
	- 如前边例子中，center作为fixed effect，提示dose不同对response有差异，而将center作为random后，dose不同对response无差异

### 4.8. 详述固定/随机效应模型，PROC GLM/MIXED

固定效应模型由以下部分组成：
（1）每个effect  factor的fixed (nonrandom) component 
（2）测量误差产生的random ‘error’ component （假定误差随机分布，独立，方差齐）‘。模型的协方差由误差项决定，如果误差满足随机独立方差齐的特点。协方差结构的矩阵特点如下，对角线都有相同值，而非对角元素都是零。。。

conducting ANOVA is via sums of squares computations, and it is the method on which PROC GLM primarily relies

PROC MIXED can analyze a fixed-effects model using a completely different approach, referred to as ‘likelihood methods’，
是一种参数估计的方法，不需要样本的独立性/方差齐性。Proc mixed也可effectively用于fixed-effects’model (without the RANDOM statement) ，与GLM方法不同的是，PROC MIXED可用于观测值非独立/方差不齐（the observations are correlated or have different variances）

----

混合效应模型由以下部分组成：
（1）每个effect  factor的fixed (nonrandom) component 
（2）one or more random components in addition to the ‘measurement error’ term. 模型的协方差由 all the random components 决定，不知误差项。由于方差不齐/变异存在，协方差矩阵的对角元素可能是不同的。由于观测间相关性/非独立，非对角元素可能是非零的。

随机效应模型/混合效应模型（有一个或更多的random effect）时，与PROC GLM相比更推荐PROC MIXED。若观测是corrected（非独立有关的）的固定效应模型（都是fixed efftct/factor），则用PROC MIXED更好。在GLM中RANDOM语句的作用仅仅是识别随机效应，并指示SAS提供相应的期望均方表，但是，在计算test时，假定所有效果都是固定的。

GLM使用方差和和均方进行F-test，MIxed采用广义线性建模，通过广义最小二乘的方法来规范协方差结构。是基于协方差结构，SAS通过识别PROC MIXED中random语句中给出的随机效应而构建的。比GLM更有效

GLM does not always produce the correct standard errors for treatment comparisons in the presence of random effects when using a CONTRAST or ESTIMATE statement。GLM不等获得两两比较时的error std。此时用PROC MIXED更合适

虽然GLM/MIXED都可以处理unbalanced cell layout的data，PROC MIXED is able to handle more complex layouts（如cell之间特别不平衡，cell有特别多的缺失） better than PROC GLM

### 4.9. 可以在PROC MIXED的RANDOM statement中指定TYPE= option，

即`specify covariance structure。`
默认选项是TYPE=VC，适用于本例中center作为random effect（仅有两个中心，每个中心病人都是independent）。
若有许多中心，一个中心内的患者之间的观察结果可能是相关的，这种相关结构，可以用TYPE=OPTION ，写在PROC MIXED的协方差矩阵中

### 4.10. PROC MIXED 默认使用restricted maximum likelihood或REML估计方差

采用restricted maximum likelihood估计的方差可能为负的，REML将此负值赋值为0。但方差为0，其他参数的估计值会受到影响，这可能导致compression的偏差。

此时可以在proc mixed中，指定method=，采用另外的比较方法，如method=ml/method=type3。在proc mixed中指定Method=type3，会给出type III的方差结果，等价于在proc glm中使用RANDOM选项




