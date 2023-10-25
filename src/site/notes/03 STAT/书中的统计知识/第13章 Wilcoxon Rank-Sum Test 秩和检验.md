---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第13章 Wilcoxon Rank-Sum Test 秩和检验/","dgPassFrontmatter":true}
---


`治疗组二分类，非正态分布非方差齐性或有序数据`  
   
## 13.1 Introduction 

秩和检验（不要求对称分布）：`类似于two-sample t-test（参数检验）`，但属于非参数检验。  

used to `compare location parameters`, such as the mean or median, between two independent populations without the assumption of normally distributed data.

可用于连续型变量的非参数检验，<font color="#92d050"><u><span style="background:#fff88f">也可用于有序的离散型变量的非参数检验（如比较两组患者的整体评估或疾病严重程度分级）</span></u></font>

## 13.3 Examples

 Seroxatene 对疼痛有缓解。28 名椎间盘突出症和症状性腿痛患者，随机接受 Seroxatene 或安慰剂治疗，response为相对于基线的疼痛总体评分（差值为正，表示疼痛缓解，差值为负，表示疼痛加重）。验证Seroxatene对神经根性背痛有作用吗?    

本例中，response为疼痛评分，数值从大到小有序（疗效从好到坏），可能数据不是正态  
执行秩和检验：proc npar1way data=xx wilcoxon。。。。  
指定秩和检验的模型（如分组变量，response变量）：class 分组变量； var resonse变量  

![../../Z appendix/Pasted image 20230211224241.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211224241.png)

![../../Z appendix/Pasted image 20230211224301.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211224301.png)

## 13.4 注意事项

（1）Wilcoxon 秩和检验，`假设两个总体分布具有相同的形状（假设相同的离散度）`，仅位置参数不同。这类似于两样本 t 检验所需的方差齐性。  

（2）SAS 中的 `PROC UNIVARIATE 来检验分布是否是正态性，无法满足正态性时，Wilcoxon 秩和检验优于两样本 t 检验`。在 PROC NPAR1WAY 中`指定 EDF 选项，通过Kolmogorov-Smirnov 检验和 Cramer-Von Mises 检验，用于检验两个总体分布是否具有相同的形状`。  

（3）秩和检验的`精确检验`（结果估计的更准）：  

![../../Z appendix/Pasted image 20230211224319.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211224319.png)

对于较大的数据集，NPAR1WAY 过程可以使用蒙特卡罗方法（Monte Carlo methods）以相当好的效率获得精确的 p 值。

（4）`provides a point estimate` in the shift in average pain scores between Seroxatene and Placebo of -1.5 with a `95% confidence interval` of 0 to -3, as seen in the resulting output

![../../Z appendix/Pasted image 20230211224436.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211224436.png)


![../../Z appendix/Pasted image 20230211224448.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211224448.png)  

