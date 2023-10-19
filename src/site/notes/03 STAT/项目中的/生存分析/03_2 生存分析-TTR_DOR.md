---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/03_2 生存分析-TTR_DOR/","dgPassFrontmatter":true}
---


## 1. 输出rtf 及编程注意事项

![../../../Z appendix/Pasted image 20230517105833.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230517105833.png)

## 2. sas代码

#统计分析/生存分析/中位_最大最小生存时间  #统计分析/生存分析/生存时间-生存率 

```SAS

/*******************************************************************************
* Program Name      : t-pfs.sas
* Programmer        : FC
* Date Created      : 2022-11-15
* Description       : To Create 表14.2.1.1.1 IRRC评估的无进展生存期（PFS）（ITT分析集）
* Client            : 康方药业有限公司
* Protocol          : AK112-301
* Input Data set(s) : ADTTE, ADSL
* Output File(s)    :
* SAS Macro(s) Used : %LogChk_IND
* Notes             :
*
* -----------------------------------------------------------------------------
* Modification History:
* <Date>           <Modifier>                   <Modifications>
* -----------------------------------------------------------------------------
*******************************************************************************/
 
** clean LOG and OUTPUT **;
DM 'OUTPUT; CLEAR; LOG; CLEAR;';
 
%macro rootpath;
    %global _rootpath _pgmname;
    %local _fpath;
 
    %let _fpath=%sysfunc(getoption(sysin));
 
    %if %length(&_fpath)=0 %then %do;
        %let _fpath=%sysget(SAS_EXECFILEPATH);
    %end;
 
    %let _rootpath=%ksubstr(%quote(&_fpath.), 1, %eval(%kindex(%quote(%upcase(&_fpath.)), %upcase(%scan(%quote(&_fpath.), -3, \)))-2));
    %let _pgmname=%scan(%quote(&_fpath.), -2, .\);
%mend rootpath;
 
%rootpath;
 
%inc "&_rootpath.\macros\msetup.sas";

proc datasets lib=work mt=data kill nolist nowarn;
quit;

proc format;
  value pval
  low-<0.001    = '<0.001'
  0.001-0.999 = [6.3]
  0.999<-high   = '>0.999'
  ;
  value pct
  	100="100"
	other=[10.1];

  invalue trt01pn
  	"AK112 20mg/kg Q3W + Chemotherapy"=1
	"Placebo + Chemotherapy"=2;
run;

proc datasets lib=work mt=data kill nolist nowarn;
quit;

%macro pfs(eval=,pgmid=,out=);
data dummy;
	length col1 $200;
	part=1;out=1;col1="达到客观缓解 (CR/PR) (%)";output;
	part=2;out=0;col1="至缓解时间 (TTR)（月）@{super [1]}";output;
	out=1;col1="  例数";output;
	out=2;col1="  均值（标准差）";output;
	out=3;col1="  中位时间";output;
	out=4;col1="  Q1，Q3";output;
	out=5;col1="  最小值，最大值";output;

	part=3;out=1;col1="缓解持续时间 (DoR)（月）@{super [2]}";output;
	out=1.1;col1="  例数";output;
	out=2;col1="  中位时间 (95% CI)";output;
	out=2.2;col1="  Q1，Q3";output;
	out=3;col1="  最小值，最大值";output;
	part=4;out=0;col1="持续缓解率 (%)@{super [2]}";output;
run;

*** Big N ***;
proc sql noprint;
	select sum(trt01pn=1) ,sum(trt01pn=2) into :pop1 trimmed,:pop2 trimmed
	from adam.adsl 
	where ittfl="Y";
quit;

%put _user_;

***达到客观缓解率***;
data adrs;
	set adam.adrs;
	part=1;
	out=1;
	where ittfl='Y' and paramcd="BOR" and parqual="&eval." and crit1fl="Y"
		and not missing(trt01pn);
run;

proc sql;
	create table adrs2 as
	select part,trt01pn,out,count(distinct usubjid) as subjnum
	from adrs
	group by part,trt01pn,out;
quit;

proc transpose data=adrs2 out=adttee2_trans(drop=_name_) prefix=_col;
	by part out;
	var subjnum;
	id trt01pn;
run;

data _null_;
	set adttee2_trans;
	call symputx('orr1',cats(_col1));
	call symputx('orr2',cats(_col2));
run;

***至缓解时间 (TTR)（月）***;
data adtteettr;
	set adam.adttee;
	where parqual="&eval." and paramcd="TTR" and ittfl="Y";
run;
 
proc means data=adtteettr nway noprint;
	class trt01pn;
	var aval;
	where parqual="&eval." and paramcd="TTR" and ittfl="Y";
	output out=ttr n=_n mean=_mean std=_std median=_median min=_min max=_max q1=_q1 q3=_q3;
run;

**中位数生存时间 90%CI;
ODS graphics;
ods output   Quantiles=median(where=(Quantile in ("50% 中位数")));
proc univariate data=adtteettr cipctldf;
	class trt01pn;
	var aval;
run;

data final_scl2;
	set median;
	length val $200 _name_2 $8 ;
	part=2;
	_name_2="n3";
	trt=input(trt01pn,??best.)+1;
	array ostat Estimate lcldistfree ucldistfree;
	array nstat $50 _est _llm _ulm;
	do over ostat;
		if ostat ne . then nstat=put(round(ostat, .01), 10.2);
		else nstat='NE';
	end;
	if _est="NE" then _est="NR";
	val=catx(' ', _est/*, cats('(', catx(', ', _llm, _ulm), ')')*/);
	keep trt part val _name_2;
run;

proc transpose data=final_scl2 out=scl(drop=_name_ rename=(_name_2=_name_)) prefix=col_;
	by part _name_2;
	var val;
	id trt;
run;


**中位数生存时间 90%CI;
/*ods select none;*/
/*ods output Quartiles = median(where=(percent in (50)));*/
/*proc lifetest data=adam.adttee alphaqt=0.05  plots=none;*/
/*	where parqual="&eval." and paramcd="TTR" and ittfl="Y";*/
/*	time aval*cnsr(1);*/
/*	strata trt01pn;*/
/*run;*/
/**/
/*ods output close;*/
/*ods select all;*/

data ttr2;
	set ttr;
    length n mean std max min median $20 n1-n5 $200;
	dec=20.1;
	num2=0.1;

    if missing(_n)=0 then n=strip(put(_n,3.));

    if missing(_mean)=0 then mean=strip(putn(round(_mean,ifn(dec<20.4,num2/10,0.0001)),strip(put(min(dec+0.1,20.4),best.))));
    if missing(_std)=0 then std=strip(putn(round(_std,ifn(dec<20.3,num2/100,0.0001)),strip(put(min(dec+0.2,20.4),best.))));
	if missing(std) and cats(n)="1" then std="NE";

	if missing(_median)=0 then median=strip(putn(round(_median,ifn(dec<20.4,num2/10,num2)),strip(put(min(dec+0.1,20.4),best.))));
	if missing(_q1)=0 then q1=strip(putn(round(_q1,ifn(dec<20.4,num2/10,num2)),strip(put(min(dec+0.1,20.4),best.))));
	if missing(_q3)=0 then q3=strip(putn(round(_q3,ifn(dec<20.4,num2/10,num2)),strip(put(min(dec+0.1,20.4),best.))));

    if missing(_min)=0 then min=strip(put(round(_min,0.01),20.2));
    if missing(_max)=0 then max=strip(put(round(_max,0.01), 20.2));

	n1=cats(n);
	n2=cats(mean)||" ("||cats(std)||")";
	n3=cats(median);
	n4=cats(q1)||", "||cats(q3);
	n5=cats(min)||", "||cats(max);

	trt=trt01pn+1;
	part=2;
run;
 
proc transpose data=ttr2 out=adttee2_ttr prefix=col;
	id trt;
	var n1-n5;
	by part;
run;

data adttee2_ttr;
	merge adttee2_ttr scl;
	by part _name_;
	if not missing(col_2) then col2=col_2;
	if not missing(col_3) then col3=col_3;
run;
******************DOR******************************************************8;
*** KM - Median Time ***;
data adttee;
	set adam.adttee;
	where parqual="&eval." and paramcd="DOR" and ittfl="Y";
	proc sort;by trt01pn;
run;

ods output Quartiles = median(where=(percent in (50)))  
	Quartiles = dorq1(where=(percent in (25)))
	Quartiles = dorq3(where=(percent in (75)));

proc lifetest data=adttee alphaqt=0.05  plots=none;
	time aval*cnsr(1);
	strata trt01pn;
run;
ods output close;

/*最大值最小值，统计方法未列出，需自行计算*/
proc sql;
  create table minmax as
  select trt01pn, cnsr, min(aval) as min, max(aval) as max from adttee
  group by trt01pn, cnsr;
quit;

data minmax1;
  merge minmax(where=(cnsr=0) rename=(min=min0 max=max0))
        minmax(where=(cnsr=1) rename=(min=min1 max=max1)); /**最大值最小值，是不是有censor考虑的生存时间*/
  by trt01pn;
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
  keep out value trt01pn;
run;

data dorq1q3;
	merge dorq1(rename=(Estimate=q1) keep=STRATUM TRT01PN Estimate) dorq3(rename=(Estimate=q3) keep=STRATUM TRT01PN Estimate);
	by STRATUM TRT01PN;
run;

data median1;
	set median(in=a) minmax1(in=b) dorq1q3(in=c);
	part=3;
	if a then out=2;
	else if c then out=2.2;
	else if b then out=3;

	array ostat Estimate LowerLimit UpperLimit q1 q3;
	array nstat $50 _est _llm _ulm _q1 _q3;
	do over ostat;
		if (a  and ostat ne .) or (c and ostat ne . ) then nstat=put(round(ostat, .01), 10.2);
		else if a or c then nstat='NE';
	end;
	if _est="NE" then _est="NR";
	if a then value=catx(' ', _est, cats('(', catx(', ', _llm, _ulm), ')'));
	if c then value=catx(', ',_q1,_q3);
	trt=trt01pn+1;
/*	keep trt part out value percent;*/
run;
 
proc sort data=median1;by part out;run;
proc transpose data=median1 out=adttee2_mediantm prefix=col;
	id trt;
	var value;
	by part out;
run;

/*持续缓解生存率*/

proc sql noprint;
    select 1*(floor(max(aval)/1)) into: max_1 trimmed
	from adttee(where=(trt01pn ne .))
	where trt01pn=1;

	select 1*(floor(max(aval)/1)) into: max_2 trimmed
	from adttee(where=(trt01pn ne .))
	where trt01pn=2;
quit;

%let max1=%sysfunc(max(&max_1,&max_2));
%put &max1 &max_1. &max_2.;

proc sort data=adttee;by trt01pn;run;

**计算第一二天;
/*proc lifetest data=adtte method=km alphaqt=0.05 plot=survival(cl  atrisk=1,2);*/
/*    time aval*cnsr(1);*/
/*    by TRT01PN;*/
/*	ods output SurvivalPlot=s5_1_1  ;*/
/*run;*/

**第3月开始，等差加3,;
ods graphics;
proc lifetest data=adttee method=km alphaqt=0.05 plot=survival(cl  atrisk=3 to 12 by 3 );
    time aval*cnsr(1);
    by trt01pn;
	ods output survivalplot=s5; 
run;

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
    by trt01pn;
	output;
	if last.trt01pn then do;
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
	 by trt01pn;
	 length sur sdf_ucl3 sdf_lcl3 survival3 /*atrisk3*/ $200.;
	 retain  sdf_ucl2 sdf_lcl2  survival2;
	 part=4;
	 if first.trt01pn then call missing(sdf_ucl2,sdf_lcl2,survival2,atrisk2);
	 if not missing(sdf_ucl) then sdf_ucl2=sdf_ucl;
     if not missing(sdf_lcl) then sdf_lcl2=sdf_lcl;
	 if not missing(survival) then survival2=survival;
	 trt=trt01pn+1;
	 if not missing(tatrisk) then do;
	     if missing(sdf_ucl2) then sdf_ucl3="NR";
		 else sdf_ucl3=strip(put(round(sdf_ucl2*100,0.0001),10.1));
	     if missing(sdf_lcl2) then sdf_lcl3="NR";
		 else sdf_lcl3=strip(put(round(sdf_lcl2*100,0.0001),10.1));
		 if missing(survival2) then survival3="NR";
		 else survival3=strip(put(round(survival2*100,0.0001),10.1));
/*         AtRisk3=strip(put(AtRisk,best.));*/
         sur=strip(survival3)||" ("||strip(sdf_lcl3)||", "||strip(sdf_ucl3)||")"/*||strip(atrisk3)*/;
		 if time >input(symget('max_'||cats(trt01pn)),??best.) then sur="NR (NE, NE)";
/*		 if sur="0.0 (0.0, 0.0)" then sur="NR (NE, NE)";*/
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

data all;
	length col2 col3 $200;
	set adttee2_:;
	if part=2 then out=input(compress(_name_,,"kd"),??best.);
	array _cl[2] _col1 _col2;
	array cl[2] col2 col3;
	array pop[2] _temporary_ (&pop1. &pop2.);
	do i=1 to 2;
		if part=1 and not missing(_cl[i]) then cl[i]=cats(_cl[i])||" ("||cats(put(_cl[i]/pop[i]*100,pct.),")");
			else if part=1 and _cl[i] in (.,0) then cl[i]="0";
	end;
	proc sort;by part out;
run;

data final;
	merge all dummy ;
	by part out;
	bign1=&pop1.;
	bign2=&pop2.;
	if part=3 and out=1.1 then do;
		col2=cats("&orr1.");
		col3=cats("&orr2.");
	end;
	if part=4 and out>0 then col1="  "||cats(out,"个月 (95% CI)");

/*	if part<=2 then pg=1;*/
/*		else pg=2;*/
	pg=1;
	proc sort;by pg part out;
run;


data _toc;
	set final;
	array cl [2] col2-col3;
	do i=1 to 2;
		if find(cl[i],"NR") then nr=1;
		if find(cl[i],"NE") then ne=1;
	end;
	if missing(nr) then nr=0;
	if missing(ne) then ne=0;
	if nr=1 or ne=1;
run;

proc sql noprint;
	select sum(nr),sum(ne) into :nr trimmed,:ne trimmed
	from _toc;
quit;

%put _user_;

%macro toc;
	%global cinenr;
	%if &nr = %then %let nr=0;
	%if &ne = %then %let ne=0;

	%if &nr ne 0 and &ne ne 0 %then %let cinenr=%str(NR = 未达到，NE = 不可评估。);
		%else %if  &nr=0 and  &ne ne 0  %then %let cinenr=%str(NE = 不可评估。);
		%else %if  &nr ne 0 and  &ne=0  %then %let cinenr=%str(NR = 未达到。);
%mend;
%toc;

data tlfds.t_14_02_&out.;
	set final;
	keep col: bign:;
	drop col_:;
run;

/* Output RTF */
ods escapechar="@";
%rtfinit(pgmname=t-ttdor,pgmid=&pgmid.,style=rtftable,outdir=&_rootpath.\prog\tlfs,bodytitle=Y);
 
proc report data=final split='|' missing
                style(column)=[asis=on just=c] style(header)=[just=c] style(report)=[width=100%];
    column pg part out col1-col3;
	define pg/order order=internal noprint;
	define part/order order=internal noprint;
	define out/order order=internal noprint;
    define col1   /display " "   style(column)=[just=l cellwidth=40%] style(header)=[just=l];
    define col2   /display "AK112+化疗|(N = &pop1)|n (%)"  style(column)=[cellwidth=30%];
    define col3   /display "安慰剂+化疗|(N = &pop2)|n (%)"  style(column)=[cellwidth=29%];

    compute after part;
        line " ";
    endcomp;

	break after pg/page;
quit;
 
ods rtf close;
ods listing;
 
%rtfpost(pgmname=t-ttdor,pgmid=&pgmid.,outdir=&_rootpath.\prog\tlfs);
%symdel cinenr;
%mend;

%pfs(eval=INDEPENDENT ASSESSOR,pgmid=1,out=03_01);
%pfs(eval=INVESTIGATOR,pgmid=2,out=03_02);

*** Log Check ***;
%LogChk_Ind;


```

