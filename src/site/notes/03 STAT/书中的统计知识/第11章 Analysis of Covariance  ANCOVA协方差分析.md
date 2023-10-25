---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第11章 Analysis of Covariance  ANCOVA协方差分析/","dgPassFrontmatter":true}
---


## 1. Introduction 

1.协方差分析（ANCOVA）：一般是，`因变量是连续型（如response），自变量是离散型（如 treatment group），再加一个或多个协变量（该协变量是连续型，一般与因变量有关系，可能正相关或负相关）--自变量与因变量的分析是ANOVA，协变量与因变量的分析是线性回归  `

2.ANCOVA，可以通过加入协变量x作为变异源，提高方法比较的精度。较之未加入协变量的ANOVA，ANCOVA结论更精确  

3.ANOVA`要求各组（如各治疗组）斜率（y-协变量线性关系）相同。`
If the assumption of equal slopes within each group is not valid, the ANCOVA methods described in this chapter cannot be used 
`assumption can be tested by including interactions such as trt*age or trt*bpdia0 in the model `

## 2. Synopsis 

数据类似如下，response variable y。自变量1：group(group1-group k--对应treatment group，离散型变量)。  
自变量2：x(连续型变量，为上introduce介绍的协变量，y与x有线性关系)。  

![../../Z appendix/Pasted image 20230211213926.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211213926.png)

应用前提：independent groups with a normally distributed response measure (y) and variance homogeneity.   

检验：主要关注的是调整协变量（X0--可能计算H0需要回归）之后的，group组间的差异是否一致（如下）：  
primary interest is the comparison of the group means（μ） adjusted to a common value of the covariate, e.g., x0. Letting μi0 represent the mean of Group i for x = x0, 

![../../Z appendix/Pasted image 20230211214023.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211214023.png)

实际 μ10 = μ20 = ... = μk0，等价于各组间y-协方差线性回归，的截距相等：H0: α1 = α2 = ... = αk 。  

`若各组斜率(各组（如各治疗组）斜率（y-协变量线性关系）)为0 ，则用one-way ANOVA更准确(说明协变量与y不是线性关系)。`如下：

![../../Z appendix/Pasted image 20230211214143.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211214143.png)

## 3. Examples 

### 3.1. （1）Example 11.1（PROC GLM，一个分组变量，一个协变量）

`用药控制胆固醇，血糖与胆固醇也有关系，将血糖作为协变量`。首次测定血糖，hemoglobin A1c levels (HbA1c) 表示`血糖水平，作为协变量x`。治疗10周，胆固醇变化百分比，作为y，比较用药组及对照组是否有差异  

The goal is to compare mean triglyceride changes between groups adjusted for HbA1c 

![../../Z appendix/Pasted image 20230211214254.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211214254.png)

![../../Z appendix/Pasted image 20230211214308.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211214308.png)

- ---
对比，若不使用协变量，只是one-way anova：

  ![../../Z appendix/Pasted image 20230211214334.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211214334.png)

以上因为：考虑了协变量的影响，把协变量的MSE从grop的MSE中拆离出来，因此协变量GROUP的最后MSE会小，F会变大

#### 3.1.1. SAS Code

![../../Z appendix/Pasted image 20230211215308.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215308.png)

![../../Z appendix/Pasted image 20230211215334.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215334.png)


![../../Z appendix/Pasted image 20230211215351.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215351.png)

![../../Z appendix/Pasted image 20230211215408.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215408.png)

### 3.2. （2）Example 11.2（PROC MIXED，多个分组变量，多个协变量）

多个分组变量，group treatment+Multi-Center   
多个协变量：年龄，研究开始时高血压的严重程度  
y：12 weeks后舒张压chg  

assumes that ：same linear relationship exists within each subgroup formed by combinations of levels of the class variables ，  
`assumption can be tested by including interactions such as trt*age or trt*bpdia0 in the model `  

![../../Z appendix/Pasted image 20230211215652.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215652.png)

#### 3.2.1. 1）LSmeans语句，/CL选项：

![../../Z appendix/Pasted image 20230211215705.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215705.png)

#### 3.2.2. 2）model语句，/solution选项：

如根据以下output，combination therapy in Center 1 ：

![../../Z appendix/Pasted image 20230211215728.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215728.png)

#### 3.2.3. 3）将center作为random effect

![../../Z appendix/Pasted image 20230211215759.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215759.png)  

![../../Z appendix/Pasted image 20230211215820.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215820.png)

![../../Z appendix/Pasted image 20230211215847.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215847.png)  

![../../Z appendix/Pasted image 20230211215907.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211215907.png)

## 4. 注意事项

### 4.1. （1）检验各组斜率是否相同：

If the assumption of equal slopes within each group is not valid, the ANCOVA methods described in this chapter cannot be used 

proc glm; 
	class trt;
	model trichg = trt hgba1c `trt*hgba1c`;
`trt*hgba1c意味着：trt分组不同的话，hgba1c不同（如hgba1c斜率不同）`

![../../Z appendix/Pasted image 20230211220014.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211220014.png)



### 4.2. （2）例11.2的案例，可构建以下模型：

![../../Z appendix/Pasted image 20230211220116.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211220116.png)

用proc mixed，可得出模型的拟合情况怎样。  
不一定加协变量多就模型好，如（5），age bpdia0可能出现共线性问题
如前，可通过AIC BIC AICC看模型拟合怎样，证明6模型拟合更好（AIC AICC BIC值更小）

![../../Z appendix/Pasted image 20230211220135.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211220135.png)
