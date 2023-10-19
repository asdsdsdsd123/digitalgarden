---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/05 生存分析-分层CMH计算ORR&DCR差值CI及p/","dgPassFrontmatter":true}
---


`有编程注意事项：95%CI分析不包括NA。两部分应该统一算法，结果一致`

![../../../Z appendix/Pasted image 20221207180148.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020221207180148.png)

## 1. sas代码

#统计分析/率的分析/proc_freq_table语句/分层strata的组间率差的95CI及p值

```sas
/*率差异 差异CI 差异p */
%macro difstat(diflabel=,difpart=,diffl=);
data adrs;
	set adam.adrs;
	part=1;
	where ittfl='Y' and paramcd="BOR" and parqual="&eval." 
		and not missing(trt01pn);
	if crit1fl="" and aval ne 6 then crit1fl="N";
		/*后面missing的不纳入分析，因此需要赋值N。NA剔除*/
	if crit2fl="" and aval ne 6 then crit2fl="N";
run;

data adrs;
	set adrs;
	where aval ne 6;
	if trtpn=1 then trtpnn=3;
		else trtpnn=trtpn;
	if cmiss(trtpnn,&diffl.)=0;
run; 

/*data misc.adrs_&diffl.;*/
/*	set adrs;*/
/*run;*/
ods select none;
proc freq data=adrs;
	tables thrand*bmrand*trtpnn*&diffl./cmh  all /*missing需要去掉，因为2x2，加missing后出现2*3*/  binomial(level='Y') commonriskdiff(cl=score);
	ods output cmh=cmh(where=(althypothesis="非零相关")) commonpdiff=compdif;
quit;
ods select all;
data adttee2_&diflabel._difstat(keep=part out col2);
	set cmh(in=a) compdif(in=b) compdif(in=c);
	length col2 $200;
	part=&difpart.;
	if a then do;out=3;col2=strip(put(prob,pval.));end;
	else if b then do;
		out=2;
		col2=tranwrd(cats("(", put(round(lowercl*100, 0.1), 10.1),", " ,put(round(uppercl*100, 0.1), 10.1),")"),",",", ");

	end;
	else if c then do;out=1;col2=cats(put(round(value*100,0.1),10.1));end;
run;
%mend;
%difstat(diflabel=orr,difpart=3,diffl=crit1fl); /*orr*/
%difstat(diflabel=dcr,difpart=5,diffl=crit2fl);/*dcr*/

data all;
	set adttee2_:;
	proc sort;by part out;
run;
```

## 2. 核心语句

> [!note]+ 核心语句
> - `Mock up要求分层(thrand*bmrand)。`*注意下table语句写的变量的顺序：stratas\*trt\*response。顺序写错不可!*
> - proc freq data=adrs;`/*需要先处理adrs，如筛选，使每人只有一种response（如加不加paramcd=BOR,影响很大。除此之外，看情况是否还需要令空的critfl为N，根据后面语句加没加missing的数据）*/`
> 	- `tables thrand*bmrand*trtpnn*&diffl./cmh  all /*missing需要去掉，因为2x2，加missing后出现2*3*/  binomial(level='Y') commonriskdiff(cl=score);`     *注意commonriskdiff选项，是该模型下总和的P CI。和riskdiff输出的情况不同，可sas试试。其中(Cl=score)，表面选择汇总评分的 Cl*
> 	- ods output `cmh`=cmh(where=(`althypothesis="非零相关"`)) `commonpdiff=compdif ; /*输出二项检验信息到数据集，里面有95% CI*/`


