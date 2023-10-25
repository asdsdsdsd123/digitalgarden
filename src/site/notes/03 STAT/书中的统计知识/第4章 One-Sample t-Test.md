---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第4章 One-Sample t-Test/","dgPassFrontmatter":true}
---

## 1. Introduction

`判断某样本总体均值与已知是否相等。`
<mark style="background: #FFB8EBA6;">某样本需满足正态分布，若总体方差未知用t分布，若总体分布已知用Z分布</mark>

通常用于`验证在不同的实验条件下平均响应是否发生变化，一般自身作为对照，即一个人多次测量，又称Paired difference t test，配对t检验。也可不matched.`

The one-sample t-test is often used in matched-pairs situations, such as testing for
pre- to post-study changes. The goal is to determine whether a difference exists between two time points or between two treatments based on data values from the same patients for both measures.

## 2. 4.2  Synopsis

样本的y满足正态分布，假设y-均值为µ0。如果y(ba) µ0二者deviation大，则表明假设不成立。检验统计量 t表示二者的deviation

The test statistic t is a function of this deviation ，t越大，倾向于拒绝原假设（y-均值为µ0）

![../../Z appendix/Pasted image 20230127161511.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161511.png)

## 3. 4.3  Examples

1）例1：在此前由Mylitech生物系统公司开展的一些男性，非胰岛素依赖型糖尿病(NIDDM)患者的I期和II期研究中，发现平均体重指数(BMI)为28.4（已知）。一名调查人员有17名男性NIDDM患者加入了一项新的研究，他们想知道这个样本的BMI是否与以前的研究结果是否一致（BMI为28.4）。非matched

![../../Z appendix/Pasted image 20230127161542.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161542.png)

![../../Z appendix/Pasted image 20230127161611.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161611.png)

![../../Z appendix/Pasted image 20230127161649.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161649.png)

![../../Z appendix/Pasted image 20230127161727.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161727.png)
2）例2：配对t检验 Paired-Difference in Weight Loss

Mylitech公司正在开发一种新的抑制食欲的化合物，用于减肥。对35名肥胖患者，在使用新化合物治疗10周前后体重测定，看体重是否有变化

![../../Z appendix/Pasted image 20230127161754.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230127161754.png)

### 3.1. sas代码

`Proc ttest 单样本t检验：`

```sas
proc ttest data = obese; 
	paired wtpre*wtpst; 
run;

proc ttest h0=28.4 data = obese , 
	var wtloss;
run;
```

## 4. 注意事项

1）Z检验(第二章)和t检验的主要区别是，z统计量是基于一个已知的标准差σ，t统计量使用样本标准差s，作为σ的估计。假设数据为正态分布，且样本量大时，方差σ2更接近于样本方差s2。可以证明，对于大样本量（n ≥ 30），t检验等价于z检验

2）检验是否满足正态分布。若不满足正态分布，t检验不一定合适，有更合适的统计方法，如秩和检验Wilcoxon signed-rank test，`可使用SAS 检查是否满足正态分布`（proc univarate `normal` xxxx;）
```sas
proc univariate normal data = diab; 
	var bmi; 
run;
```

3）用SAS`输出t分布的统计量t的临界值（之前查表），p值`

通过以下函数计算t统计量：QUANTILE(‘T’,0.975,15)；其中‘T’代表t的临界值，0.975=1-α（单侧的α为0.025），15=n-1

通过以下函数计算p值：1-PROBT(t,υ)，t为t分布临界值t，υ为n-1；如例一的双侧p值，2*(1–PROBT(2.23,15))；例2的单侧p值，1–PROBT(3.226,34).