---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/01 生存分析-Kaplan-Meier估计生存时间及not-event率/","dgPassFrontmatter":true}
---


## 1. 输出rtf 及编程注意事项

![../../../Z appendix/Pasted image 20221209223943 2.png](/img/user/Z%20appendix/Pasted%20image%2020221209223943%202.png)

## 2. 中位时间，最大最小值

#统计分析/生存分析/中位_最大最小生存时间

```sas
*** KM - Median Time ***;
ods graphics;
ods trace on;
ods output Quartiles = median/*(where=(percent in (25 50 75)))*/;
proc lifetest data=adttee alphaqt=0.05  plots=none;
	time aval*cnsr(1);
	strata trtpn;
run;
ods output close;
ods select all;

/*最大值最小值，统计方法未列出，需自行计算*/
proc sql;
  create table minmax as
  select trtpn, cnsr, min(aval) as min, max(aval) as max from adttee
  group by trtpn, cnsr;
quit;

data minmax1;
  merge minmax(where=(cnsr=0) rename=(min=min0 max=max0))
        minmax(where=(cnsr=1) rename=(min=min1 max=max1)); /**最大值最小值，是不是有censor考虑的生存时间*/
  by trtpn;
  out=4;
  minval=min(min0, min1);
  maxval=max(max0, max1);
  if minval=min1 then cmin=cats(put(round(minval, .01), 10.2), '*');
  else if minval=min0 then cmin=cats(put(round(minval, .01), 10.2));
  if min0=min1 then put 'WARNING: Min AVAL are the same across diff cnsr';

  if maxval=max1 then cmax=cats(put(round(maxval, .01), 10.2), '*');
  else if maxval=max0 then cmax=cats(put(round(maxval, .01), 10.2));
  if max0=max1 then put 'WARNING: Max AVAL are the same across diff cnsr';
  value=catx(', ', cmin, cmax);
  keep out value trtpn;
run;


data median1;
	set median(in=a) minmax1;
	part=2;
	if percent=25 then out=1;
	else if percent=50 then out=2;
	else if percent=75 then out=3;

	array ostat Estimate LowerLimit UpperLimit;
	array nstat $50 _est _llm _ulm;
	do over ostat;
		if a and ostat ne . then nstat=put(round(ostat, 0.01), 10.2);
		else if a then nstat='NE';
	end;
	if a and _est="NE" then _est="NR";
	if a then value=catx(' ', _est, cats('(', catx(', ', _llm, _ulm), ')'));
	trt=trtpn+1;
	keep trt part out value percent;
run;
 
proc sort data=median1;by part out;run;
proc transpose data=median1 out=adttee2_mediantm prefix=col;
	id trt;
	var value;
	by part out;
run;
```


### 2.1. sas核心代码

> [!note]+ sas核心代码
> - 中位生存时间及生存时间最大/最小值：
> 	- `proc lifetest`;
> 		- `time aval\*cnsr(1)`;
> 		- `strata trtpn`;
> 		- ods output `Quartiles =` median;


## 3. no-event率（如：无进展生存期，总生存率）

#统计分析/生存分析/生存时间率

例如：以3为间隔，3 6 9这样展示

```sas
proc sql noprint;
    select 3*(floor(max(aval)/3)) into: max1 trimmed
	from adttee(where=(trt01pn ne .));
quit;
%put &max1;

proc sort data=adttee;by trtpn;run;

**计算第一二天;
/*proc lifetest data=adtte method=km alphaqt=0.05 plot=survival(cl  atrisk=1,2);*/
/*    time aval*cnsr(1);*/
/*    by TRT01PN;*/
/*	ods output SurvivalPlot=s5_1_1  ;*/
/*run;*/

**第3月开始，等差加3,;
ods select none;
proc lifetest data=adttee method=km alphaqt=0.05 plot=survival(cl  atrisk=3 to &max1. by 3 );
    time aval*cnsr(1);
    by trtpn;
	ods output survivalplot=s5; 
run;
ods select all;

/***第18天以后，等差加6,到30;*/
/*proc lifetest data=adtte method=km alphaqt=0.05 plot=survival(cl  atrisk=18 to 24 by 6 );*/
/*    time aval*cnsr(1);*/
/*    by TRT01PN;*/
/*	ods output SurvivalPlot=s5_1_3  ;*/
/*run;*/
/**/
/***第24天以后，等差加30,到30;*/
/*proc lifetest data=adtte method=km alphaqt=0.05 plot=survival(cl  atrisk=30 to 60 by 30 );*/
/*    time aval*cnsr(1);*/
/*    by TRT01PN;*/
/*	ods output SurvivalPlot=s5_1  ;*/
/*run;*/

data s5_1;
    set s5;
    by trtpn;
	output;
	if last.trtpn then do;
	    if .<tatrisk<&max1. then do;
		    diff=(&max1.-tatrisk)/3;
			if diff>0 then do;
				do i=1 to diff;
				    tatrisk=tatrisk+3;
					output;
				end;
			end;
		end;
	end;
run;

data s5_2(keep=part trt tatrisk sur) riskd1(keep=part trt tatrisk survival2);
     set s5_1;
	 by trtpn;
	 length sur sdf_ucl3 sdf_lcl3 survival3 /*atrisk3*/ $200.;
	 retain  sdf_ucl2 sdf_lcl2  survival2;
	 part=7;
	 if first.trtpn then call missing(sdf_ucl2,sdf_lcl2,survival2,atrisk2);
	 if not missing(sdf_ucl) then sdf_ucl2=sdf_ucl;
     if not missing(sdf_lcl) then sdf_lcl2=sdf_lcl;
	 if not missing(survival) then survival2=survival;
	 trt=trtpn+1;
	 if not missing(tatrisk) then do;
	     if missing(sdf_ucl2) then sdf_ucl3="NR";
		 else sdf_ucl3=strip(put(round(sdf_ucl2*100,0.0001),10.1));
	     if missing(sdf_lcl2) then sdf_lcl3="NR";
		 else sdf_lcl3=strip(put(round(sdf_lcl2*100,0.0001),10.1));
		 if missing(survival2) then survival3="NR";
		 else survival3=strip(put(round(survival2*100,0.0001),10.1));
/*         AtRisk3=strip(put(AtRisk,best.));*/
         sur=strip(survival3)||" ("||strip(sdf_lcl3)||", "||strip(sdf_ucl3)||")"/*||strip(atrisk3)*/;
/*		 if atrisk=0 then sur="NR (NR, NR)";*/
		 /*else sur="NE (NE, NE) / NE";*/
		 output;
	 end;
	proc sort;by part tatrisk;
run;

proc sort data=s5_2;
    by part tatrisk;
run;

proc transpose data=s5_2 out=adttee2_sp(drop=_name_ rename=(tatrisk=out)) prefix=col;
    id trt;
	var sur;
	by part tatrisk;
run;

```

### 3.1. sas核心代码

> [!note]+ sas核心代码
> - proc lifetest data=adttee `method=km /*默认KM*/` alphaqt=0.05 
> - `plot=survival(cl  atrisk=3 to &max1. by 3 )`
> 	- time aval\*cnsr(1);
> 	- `by trt`;`/*感觉也可也参照在之前strata trt?*/`
> 	- ods output `SurvivalPlot=xx`

### 3.2. 输出no-event的数据集，及扩展详解

![../../../Z appendix/Pasted image 20221209225740.png](/img/user/Z%20appendix/Pasted%20image%2020221209225740.png)

````ad-note
collapse: open
title: 理解

sas

```sas
proc lifetest data=adtte method=km alphaqt=0.05
             plot=survival(cl  atrisk=1,2,3,6,9,12,18,24,30,60,90,120);

    time aval*cnsr(1);
    by TRT01PN;  /*感觉可以写成strata trt01pn，效果一样，生成的StratumNum=trt01pn*/
	ods output Quartiles = median SurvivalPlot=stotal  ;
 /* 输出中位数， 输出生存曲线每个时点的tatrisk的概率*/

run;

data stotal2;   /*可结合上面的图理解*/
	set stotal;
	length p up low 8;
	retain p up low;

	if not missing(Survival) then p=Survival;
	if not missing(SDF_UCL) then up=SDF_UCL;
	if not missing(SDF_LCL) then low=SDF_LCL;
	if not missing(tAtRisk);

run;

**如trt01an=1时，最终tAtRisk只到30（p=0 wp=0 low=0,说明这个治疗组，发生时间没有超过30天的，按照mock up没有60 90 120天的统计结果

这时候需要dummy）;

data dummy;

	do trt01pn=1,2,9;
		do tAtRisk=1,2,3,6,9,12,18,24,30,60,90,120;
			output;
		end;
	end;

run;

data stotal3;
	merge stotal2 dummy;
	by trt01pn tAtRisk;

run;
```
````

> [!note]+ dummy数据集及output如下图
> - ![../../../Z appendix/Pasted image 20221209230600.png](/img/user/Z%20appendix/Pasted%20image%2020221209230600.png)
> - ![../../../Z appendix/Pasted image 20221209230609.png](/img/user/Z%20appendix/Pasted%20image%2020221209230609.png)



