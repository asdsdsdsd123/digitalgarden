---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第10章 Linear Regression/","dgPassFrontmatter":true}
---


## 1. Introduction 

回归分析用于分析响应y和定量因素x之间的关系。了解这种关系对于从已知的x值预测未测量的响应非常重要。
可以通过回归分析，确定线性关系或相关的重要性，预测未来的响应，估计预期x值的平均响应，并对回归线的斜率进行推断。

## 2. Synopsis 

最小二乘法，

![../../Z appendix/Pasted image 20230211174155.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174155.png)

## 3. Examples

### 3.1. (1) Examples 10.1

心绞痛患者在服用实验性抗心绞痛药物前和4周后进行跑步机测试，欲了解运动时间的改善是否与患者的病史有关。x为疾病持续时间（年为单位），y为运动时间提高的百分比

![../../Z appendix/Pasted image 20230211174212.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174212.png)

![../../Z appendix/Pasted image 20230211174222.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174222.png)

![../../Z appendix/Pasted image 20230211174305.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174305.png)

![../../Z appendix/Pasted image 20230211174338.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174338.png)

Multiple Linear Regression --一个response vaiable:y，多个quantitative varable:x(定量变量)  
Prog reg

### 3.2. (2) Examples 10.2

先给中重度脱水儿童，不同剂量的电解质溶液，后通过量表衡量脱水恢复程度，看不同剂量电解质对脱水回复有无关系。除此之外，还记录了每人的年龄，体重

![../../Z appendix/Pasted image 20230211174441.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174441.png)

第一个model语句，加了许多explanatory variable，有时R2（模型可解释多少百分比的变异）比只有单个explanatory variable的大；或整体模型更倾向于非线性  
第二个model语句，simple linear regression ，结果R2较第一个Model语句小  
最后两个model语句，R2与第一个相差不大，表明，如果模型包含age或wt，再加入dose，则作用不大  

## 4. 注意事项

### 4.1. 1.绘制置信区间图【在proc glm/proc reg中添加选项plots=all/FITPLOT ，或plots(only)=FITPLOT】

如预测的y的95%置信区间：对于给定x（记为xp），其对应的y有95%概率在以下范围内：

![../../Z appendix/Pasted image 20230211174506.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174506.png)

### 4.2. 2.线性回归假设：experimental region下，error方差一致。

从残差（y真值-y预测）图可知（plots=all/RESIDUALS），残差没有规律，0上下均有分布，证明假设成立。如：

![../../Z appendix/Pasted image 20230211174523.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174523.png)

### 4.3. 3.从图判断曲线拟合好坏，找出ouerliner：

对于非线性数据进行线性拟合，判断线性拟合模型的好坏：

![../../Z appendix/Pasted image 20230211174537.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174537.png)

输出以上统计量到数据集，因为都是来自拟合诊断panel，可写到一个数据集里（如outstat）
output out=outstat p=Predicted  r=Residual stdr=se_resid student=RStudent h=Leverage cookd=CooksD;
 outstat 为输出的数据集名称 ，指定p r stdr student h cookd为拟合诊断的统计量keyworld   

### 4.4. 5.其他类型的回归（如model是二次方程的回归）

![../../Z appendix/Pasted image 20230211174558.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174558.png)

### 4.5. 6.independent observations of 线性回归：

如果x表示时间，而响应y表示同一实验单元(如患者)在不同时间点的测量值，则x 与 y的线性回归分析，则违背了独立性的假设。
可采取类似前重复测量的方法。时间上的相关性，在时间序列分析中被称为“自相关性”。
例10.1，Proc glm中，output的这部分，表示自相关性怎样。其数值接近2，表明没有显著的自相关（理解为数据独立）

![../../Z appendix/Pasted image 20230211174628.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174628.png)

也可在prog reg中加选项，也输出Durbin-Watson test 部分（大块标黄的内容），using the DW option in 
the MODEL statement to check for autocorrelation.（自回归）

### 4.6. 7.多自变量的线性回归，检验自变量间的共线性：model xxxx/vif collinnoint。

如果结果显示，某两变量共线性，最后model中就不能都加上改两变量
（output间如下图标黄的位置）
VIF，方差膨胀大，倾向于表示共线性
共线性判断，特征值，数值越小越倾向于共线性

![../../Z appendix/Pasted image 20230211174723.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211174723.png)

### 4.7. 8.很神奇，proc reg和proc mixed/glm的结果，可以一致。有些线性的结果可以用proc mixed/glm输出

`prog_reg回归与proc_mixed/proc glm可实现相同效果`

1）Ignoring all group factors, determine if the response variable SCORE is linearly related to Age
proc glm data = examp.trial;
	model score = age / solution;run;
quit;

proc reg;
    model score=age/solution;
run;
quit;

这两种结果一致

2）`Determine if the slope` of the linear regression in 1) `differs between treatment groups`
proc glm data = examp.trial;
	class trt;
	model score = trt age `trt * age`;
	/* use `interaction` to check for equal slopes  * /
run;
quit;

3）`Estimate the mean response` for each treatment group using Age as a covariate
proc mixed data=examp.trial;
    class trt;
    model score=trt age/solution;
run;

4）Compare treatment group means adjusted for Age and Study Center
proc mixed data=examp.trial;
    class trt center;
    model score=trt center age/solution;
run;

