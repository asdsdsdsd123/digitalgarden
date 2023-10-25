---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第21章 The Log-Rank Test生存分析，不带协变量/","dgPassFrontmatter":true}
---

## 1. Introduction

`log-rank test` is a statistical method for `comparing distributions of time until the occurrence of an event` of interest among independent groups    
time指生存时间：time which occurrence of an event  

log-rank test a method of `comparing ‘risk-adjusted’ event rates`，当`数据中出现censored data（截止数据，到结束日期时，仍没有事件发生）时，用log-rank test更有用。`若每个人在最后均发生了event，没有censored data，用Wilcoxon rank-sum test 也可。Wilcoxon rank-sum test can be highly biased in the presence of censored data.

log-rank test的零假设：Similar risk-adjusted event rates among groups not only for the clinical trial as a whole， but also for any arbitrary time point during the trial

## 2. Example

数据如下：

![../../Z appendix/Pasted image 20230219183011.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183011.png)

---

![../../Z appendix/Pasted image 20230219183032.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183032.png)

```sas
ods graphics on;
proc lifetest data = hsv    
     plots=(survival(atrisk=5 10 15 20 25 30 35 40 45 50 55 60) ls); /*绘制生存曲线 指定tatrisk(faliure times)概率，图下有颜色字体为某个faliure times(某时未到event)人数
及negative log of the event times曲线*/
    time wks*cens(1);  /*wks为生存时间，cens=1时表示该观测为censored data*/
    strata vac;             /*分组变量为vac*/
run;
ods graphics off;
```

![../../Z appendix/Pasted image 20230219183103.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183103.png)  

![../../Z appendix/Pasted image 20230219183113.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183113.png)  

<span style="background:#fff88f">其他output:</span>

![../../Z appendix/Pasted image 20230219183129.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183129.png)

## 3. 注意事项

### 3.1. KM法的生存分析，可在proc lifetest后加method=KM
### 3.2. SAS输出的几种结果：

![../../Z appendix/Pasted image 20230219183144.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183144.png)

### 3.3. It is easy to see that the techniques of the log-rank test can be used to compare event times among any group factor with multiple levels

### 3.4. have strata factor/characteristic covariate under treatment group

![../../Z appendix/Pasted image 20230219183158.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183158.png)

---

也可使用numeric covariate，不过没有Cox regression analyses好用

![../../Z appendix/Pasted image 20230219183209.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183209.png)

把协变量x（试验开始前一段时间，发作总次数）认为分成四组，this adjustment does not take into account the ordinal structure of the categories of X
(i)X<8, (ii) 8<X<10, (iii) 10<X<12, and (iv) X>12。再用LIFETEST procedure 


![../../Z appendix/Pasted image 20230219183221.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219183221.png)

