---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/04 生存分析-Clopper-Pearson计算ORR&DCR 的95 CI/","dgPassFrontmatter":true}
---


## 1. 输出rtf 及编程注意事项

`有编程注意事项：95%CI分析不包括NA`

![../../../Z appendix/Pasted image 20221207180148 1.png](/img/user/Z%20appendix/Pasted%20image%2020221207180148%201.png)

## 2. sas代码

#统计分析/率的分析/proc_freq_table语句/各组率的95CI

```sas
***客观缓解率/及疾病控制率+对应95% CI***;
/*客观缓解率*/
proc sql noprint;
	select count(distinct usubjid) into :pop3-
	from adrs
	where trt01pn>. and aval ne 6
	group by trt01pn;
quit;

option mprint;
%macro ordor_ci(label=orr,part=2,fl=crit1fl);

proc sql;
	create table &label. as
	select &part. as part,1 as out,trt01pn,count(distinct usubjid) as subjnum
	from adrs
	where &fl.="Y" and aval ne 6
	group by trt01pn;
quit;

proc transpose data=&label. out=adttee2_&label.(drop=_name_) prefix=_col;
	by part out;
	var subjnum;
	id trt01pn;
run;

/*95% CI*/
proc sql;
	create table  &label.ci as
	select &part. as part,2 as out,trt01pn,&fl.,count(distinct usubjid) as subjnum
	from adrs
	where aval ne 6
	group by trt01pn,&fl.;
quit;

proc freq data=&label.ci noprint;
  by part out trt01pn;
  table crit1fl/binomial(level='Y' exact) missing;
  weight subjnum/zeros; 
  output out=&label.ci2 binomial;
run;

***;
/*统计了每种情况的频数用weight，没有统计不需要proc freq中的weight语句*/

/*proc freq data=adrs;*/
/*	tables thrand*bmrand*trtpnn*&diffl./cmh  all /*missing需要去掉，因为2x2，加missing后出现2*3*/  /*binomial(level='Y') commonriskdiff(cl=score);*/
/*	ods output cmh=cmh(where=(althypothesis="非零相关")) commonpdiff=compdif;*/
/*quit;*/
***;
data &label.ci3;
  set &label.ci2;
  if n ne 0 then cl=tranwrd(cats("(",put(round(xl_bin*100, 0.1), 10.1),", " ,put(round(xu_bin*100,0.1), 10.1),")"),",",", ");
  else cl='NE, NE';
  trt=trt01pn+1;
  keep part out trt01pn  trt cl ;
run;

proc transpose data=&label.ci3 out=adttee2_&label.ci prefix=col;
  by part out ;
  var cl;
  id trt;
run;

%mend;
%ordor_ci(label=orr,part=2,fl=crit1fl);/*orr*/
%ordor_ci(label=dcr,part=4,fl=crit2fl);/*dcr*/
```

## 3. 核心语句

> [!note]+ 核心语句
> - `看mock up都是未分层的`
> - proc freq data=adrs;`/*需要先处理adrs，如筛选，使每人只有一种response（如加不加paramcd=BOR,影响很大。除此之外，看情况是否还需要令空的critfl为N，根据后面语句加没加missing的数据）*/`
> 	- by part out trt01pn; `/*每个用药组输出*/`
> 	- table crit1fl/`binomial(level='Y' exact)` `missing`; `/*需指定level=xx，否则统计结果可能不是阳性率*/`
> 	- `weight subjnum/zeros;` `/*如果数据是汇总的，需要加该语句。不是汇总的可以不加weight语句*/`
> 	- output out=&label.ci2 `binomial`; `/*输出二项检验信息到数据集，里面有95% CI*/`
> - ---
> - [[https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_freq_sect012.htm\|https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_freq_sect012.htm]]
> 	- The *WEIGHT statement*  names a numeric variable that provides a weight for each observation in the input data set.If you use a WEIGHT statement, PROC FREQ assumes that an observation represents n observations, where n is the value of variable.
> 	- If the value of the WEIGHT variable is missing, PROC FREQ does not use that observation in the analysis. If the value of the WEIGHT variable is zero, PROC FREQ ignores the observation unless you specify the *ZEROS* option









