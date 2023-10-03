---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["003-STAT/书中的统计知识"],"permalink":"/003-STAT/书中的统计知识/第18章 McNemar’s Test配对卡方/","dgPassFrontmatter":true}
---

## 1. Introduction 

McNemar’s Test：配对样本的卡方检验，如`同一个病人`按照不同条件（如给药不同，时间/部位不同），`进行两次检测所得结果。目的是比较两组条件或匹配的反应率是否不同`
2xr的用 Stuart-Maxwell test

## 2. Examples

### 2.1. Examples 1，2x2的配对卡方检验（McNemar’s Test）

研究前后的临床试验显示，总胆红素值异常高(高于正常范围的上限)，如表18.2。验证治疗前后的异常率是否发生了变化?

![../../Z appendix/Pasted image 20230217173818.png](/img/user/Z%20appendix/Pasted%20image%2020230217173818.png)  

![../../Z appendix/Pasted image 20230217173832.png](/img/user/Z%20appendix/Pasted%20image%2020230217173832.png)  

```sas
proc format;
	value rsltfmt 0 = 'N' 1 = 'Y';
run;

ods output mcnemarstest=mc crosstabfreqs=cross;
proc freq data = bili;
    tables pre*pst / agree norow nocol nopercent;
    exact mcnem;
    format pre pst rsltfmt.;
title1 "McNemar's Test";
title2 "Example 18.1: Bilirubin Abnormalities Following
Drug Treatment";
run;
```

![../../Z appendix/Pasted image 20230217173916.png](/img/user/Z%20appendix/Pasted%20image%2020230217173916.png)

### 2.2. Examples 2，2xr的配对卡方检验（Stuart-Maxwell test，=SAS中的 Bhapkar’s test）

对某些高脂肪食品的渴望描述为“从不”、“偶尔”或“经常”，比较饮食疗法后对某些高脂肪食品的渴望率是否有变化

```sas
proc catmod data=diet;
    weight cnt;
    response marginals;
    model pre*wk2=_response_/freq;
    repeated time 2;  /*表明配对的pre与wk2为重复测量的*/
run;
quit;
```

![../../Z appendix/Pasted image 20230217174101.png](/img/user/Z%20appendix/Pasted%20image%2020230217174101.png)

indicating a significant difference in the distribution of responses over time

## 3. 注意事项
通过proc freq输出对称性检验：（当level>2，即非2x2的表时，会自动输出）

![../../Z appendix/Pasted image 20230217174137.png](/img/user/Z%20appendix/Pasted%20image%2020230217174137.png)

上图中，因为不是2x2的布局，因此加上exact mcnem没有意义

