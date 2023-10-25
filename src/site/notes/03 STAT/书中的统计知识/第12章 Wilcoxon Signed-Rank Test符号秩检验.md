---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第12章 Wilcoxon Signed-Rank Test符号秩检验/","dgPassFrontmatter":true}
---


## 12.1 Introduction 

`符号秩检验（要求对称分布）：类似于one-sample t-test（参数检验）`，但属于`非参数检验  `

`参数检验：总体分布类型（如正态分布）已知`，对其未知参数进行检验。如t-test和ANOVA，ANCOVA，都是基于总体分布为正态分布，总体方差相等的前提下，对总体均值进行检验

`非参数检验`：总体分布未知或已知总体分布与检验所要求的条件不符，经数据转换也不能满足参数检验的条件，这时需要用不依赖于总体分布形式的检验方法-->`检验两组总体位置（如按排序后的秩次）是否相同`

## 12.3 Examples 
（1）24名巩膜炎（KCS）的患者为对象，评价试验药物对改善眼部干燥的效果，response为玫瑰-孟加拉的染色分数（分数高，坏死多）。患者一只眼使用眼部湿润剂试验药物，另一只眼睛中使用对照药物。评价试验药物和对照药物的差异

![../../Z appendix/Pasted image 20230211222853.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211222853.png)

![../../Z appendix/Pasted image 20230211222904.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211222904.png)

![../../Z appendix/Pasted image 20230211222919.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211222919.png)

![../../Z appendix/Pasted image 20230211222933.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211222933.png)

### SAS Code

![../../Z appendix/Pasted image 20230211223013.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230211223013.png)


## 12.4 注意事项 

见书中
