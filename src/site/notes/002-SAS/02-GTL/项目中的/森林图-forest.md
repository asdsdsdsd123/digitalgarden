---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["002-SAS/02-GTL/项目中的"],"permalink":"/002-SAS/02-GTL/项目中的/森林图-forest/","dgPassFrontmatter":true}
---


# 1. 输出结果

![../../../Z appendix/Pasted image 20221217121041.png](/img/user/Z%20appendix/Pasted%20image%2020221217121041.png)

![../../../Z appendix/Pasted image 20221217121147.png](/img/user/Z%20appendix/Pasted%20image%2020221217121147.png)

---

# 2.统计分析产生数据

## 1. 生存分析产生数据（图一）-sas代码：

```sas

proc format;
  value pct
  	100="100"
	other=[10.1];

  invalue ord
  	"<65岁"=1
	">=65岁"=2
	"男性"=3
	"女性"=4
	"IIIB"=5
	"IIIC"=6
	"IV"=7
	"<3"=8
	"≥3"=9
	"存在"=10
	"不存在"=11
	"有"=12
	"无"=13
	"0"=14
	"1"=15
	"19Del"=16
	'L858R'=17
	'阴性'=18
	'阳性'=19
	'一线'=20
	'二线'=21;
run;

proc datasets lib=work mt=data kill nolist nowarn;
quit;

/*%macro pfs(eval=,paramcd=,out=);*/
data dummy;
	length col1 $200;
	part=1;ord=.;col1="年龄";output;
	ord=1;col1="  <65岁";output;
	ord=2;col1="  >=65岁";output;

	part=2;ord=.;col1="性别";output;
	ord=3;col1="  男性";output;
	ord=4;col1="  女性";output;

	part=3;ord=.;col1="入组时临床分期";output;
	ord=5;col1="  IIIB";output;
	ord=6;col1="  IIIC";output;
	ord=7;col1="  IV";output;

	part=4;ord=.;col1="基线远处转移部位个数";output;
	ord=8;col1="  <3";output;
	ord=9;col1="  >=3";output;

	part=5;ord=.;col1="基线脑转移";output;
	ord=10;col1="  存在";output;
	ord=11;col1="  不存在";output;

	part=6;ord=.;col1="吸烟史";output;
	ord=12;col1="  有";output;
	ord=13;col1="  无";output;

	part=7;ord=.;col1="基线ECOG评分";output;
	ord=14;col1="  0";output;
	ord=15;col1="  1";output;

	part=8;ord=.;col1="基线EGFR突变";output;
	ord=16;col1="  19Del";output;
	ord=17;col1="  L858R";output;

	part=9;ord=.;col1="T790M突变状况";output;
	ord=18;col1="  阴性";output;
	ord=19;col1="  阳性";output;

	part=10;ord=.;col1="既往接受EGFR-TKI治疗";output;
	ord=20;col1="  一线";output;
	ord=21;col1="  二线";output;

	part=11;ord=.;col1="肝转移";output;
	ord=10;col1="  存在";output;
	ord=11;col1="  不存在";output;
run;

*** Big N ***;
proc sql noprint;
	select sum(trt01pn=1) ,sum(trt01pn=2) into :pop1 trimmed,:pop2 trimmed
	from adam.adsl 
	where ittfl="Y";
quit;

%put _user_;

data adttee;
	merge adam.adttee(in=a) adam.adsl(keep=usubjid erfstag metnum smokstt egfrfl1 egfrfl2 tmmul linnum livemet);
	by usubjid;
	if a and( ittfl='Y' and paramcd="PFS" and parqual="INDEPENDENT ASSESSOR" 
		and not missing(trt01pn));
	if trt01pn=1 then trt01pn=3; /*HR比值有方向，令治疗组值大些*/
	length agegr1_ sex_ erfstag_ metnum_ bralomet_ smokstt_ ecogbl_ egfr1 egfr2 tmmul_ linnum_ livemet_ $200;

	if not missing(agegr1) then agegr1_=cats(agegr1)||"岁";
	if not missing(sex) then sex_=ifc(sex="F","女性","男性");
	if find(erfstag,"IV") then erfstag_="IV";
		else erfstag_=erfstag;
	if erfstag_ not in ("IIIB","IIIC","IV") then erfstag_="";

	if metnum in ("1","2") then metnum_="<3";
		else if metnum in("3",">3") then metnum_="≥3";
	if bralomet="Y" then bralomet_="存在";
		else bralomet_="不存在";
	if smokstt in ("目前吸烟","曾经吸烟") then smokstt_="有";
		else if
{ #missing}
(smokstt) and smokstt="从未吸烟" then smokstt_="无";
	if ecogbl in ("0","1") then ecogbl_=ecogbl;
	if egfrfl1="Y" then egfr1="19Del";
	if egfrfl2="Y" then egfr2="L858R";
	if tmmul not in ("未知","") then tmmul_=tmmul;
	if linnum ne "" then linnum_=linnum;
	if livemet="Y" then livemet_="存在";
		else livemet_="不存在";
run;

%macro strata(part=1,stra=agegr1_);
proc sql noprint;
	create table ntot as
	select trt01pn,&stra.,count(distinct usubjid) as ntot
	from adttee 
	where not missing(&stra.)
	group by trt01pn,&stra.;
quit;


proc sql;
	create table adttee2 as
	select &part. as part,trt01pn,&stra.,count(distinct usubjid) as subjnum
	from adttee
	where cnsr=0 and  not missing(&stra.)
	group by part,trt01pn,&stra.
	order by trt01pn,&stra.;
quit;

data adttee2_;
	merge adttee2 ntot;
	by trt01pn &stra.;
	length val $200;
	if cmiss(subjnum,ntot)=0 then val=cats(subjnum,"/",ntot);
	else if missing(subjnum) then val=cats("0","/",ntot);
	else if cmiss(subjnum,ntot)=2 then val=cats("0","/","0");
	part=&part.;
	proc sort;by part &stra.;
run;

proc transpose data=adttee2_ out=adttee3 prefix=_col;
	by part &stra.;
	id trt01pn;
	var val;
run;

proc sort data=adttee out=adttee_use;by &stra.;run;

ods select none;
ods output ParameterEstimates = est;
proc phreg data=adttee_use;
	by &stra.;
	where  not missing(&stra.);
	model aval*cnsr(1)=trt01pn/ties=exact rl=wald alpha=.05;
	 %if &part ne 5 and &part ne 10 %then strata thrand bmrand;;
							%if &part=5 %then strata thrand;;
							%if &part=10 %then strata bmrand;;
	hazardratio trt01pn;
run;
ods select all;

proc sort data=adttee3;by &stra.;run;
data strata1_&stra.;
	length col1-col4 $200;
	merge adttee3 est(keep=&stra. hazardratio hrlowercl hruppercl);
	by &stra.;
	if not missing(&stra.) then ord=input(&stra.,ord.);
	if not missing(hazardratio) then hr=cats(put(round(hazardratio,0.01),10.2));
		else hr="NE";
	if not missing(hruppercl) then hrh=cats(put(round(hruppercl,0.01),10.2));
		else hrh="NE";
	if not missing(hrlowercl) then hrl=cats(put(round(hrlowercl,0.01),10.2));
		else hrl="NE";
	col1=&stra.;
	if missing(_col2) then _col2="0/0";
	if missing(_col3) then _col3="0/0";
	col2=_col3;
	col3=_col2;
	col4=cats(hr)||" ("||catx(", ",hrl,hrh)||")";
	proc sort;by ord;
	

run;
%mend;

%strata(part=1,stra=agegr1_);
%strata(part=2,stra=sex_);
%strata(part=3,stra=erfstag_);
%strata(part=4,stra=metnum_);
%strata(part=5,stra=bralomet_);
%strata(part=6,stra=smokstt_); /**/
%strata(part=7,stra=ecogbl_);
%strata(part=8,stra=egfr1);
%strata(part=8,stra=egfr2);
%strata(part=9,stra=tmmul_);

%strata(part=10,stra=linnum_);
%strata(part=11,stra=livemet_);

data all;
	set strata1:;
	proc sort;by part ord;
run;

data final;
	merge all dummy(in=a) ;
	by part ord;
	if a;
	array cl col2-col3;
	do i=1 to 2;
		if ord ne . and missing(cl[i]) then cl[i]="0/0";  
	end; 
	if ord ne . and missing(col4) then col4="NE (NE, NE)";  
	order2=34-_n_+1;/*标记放置位置，y，注意是倒序，图呈现顺序和table不一致*/
	constant=0;/*标记放置位置，x*/
	proc sort;by order2;
run;

```

[[../../../03 STAT/项目中的/生存分析/03 生存分析-分层（不分层）Cox模型求风险比HR及其CI.md\|HR统计方法参考这部分]]

## 2. CMH分析产生数据（图二）-sas代码

```sas
data dummy;
	length col1 $200;
	part=1;ord=.;col1="年龄";output;
	ord=1;col1="  <65岁";output;
	ord=2;col1="  >=65岁";output;

	part=2;ord=.;col1="性别";output;
	ord=3;col1="  男性";output;
	ord=4;col1="  女性";output;

	part=3;ord=.;col1="入组时临床分期";output;
	ord=5;col1="  IIIB";output;
	ord=6;col1="  IIIC";output;
	ord=7;col1="  IV";output;

	part=4;ord=.;col1="基线远处转移部位个数";output;
	ord=8;col1="  <3";output;
	ord=9;col1="  >=3";output;

	part=5;ord=.;col1="基线脑转移";output;
	ord=10;col1="  存在";output;
	ord=11;col1="  不存在";output;

	part=6;ord=.;col1="吸烟史";output;
	ord=12;col1="  有";output;
	ord=13;col1="  无";output;

	part=7;ord=.;col1="基线ECOG评分";output;
	ord=14;col1="  0";output;
	ord=15;col1="  1";output;

	part=8;ord=.;col1="基线EGFR突变";output;
	ord=16;col1="  19Del";output;
	ord=17;col1="  L858R";output;

	part=9;ord=.;col1="T790M突变状况";output;
	ord=18;col1="  阴性";output;
	ord=19;col1="  阳性";output;

	part=10;ord=.;col1="既往接受EGFR-TKI治疗";output;
	ord=20;col1="  一线";output;
	ord=21;col1="  二线";output;

	part=11;ord=.;col1="肝转移";output;
	ord=10;col1="  存在";output;
	ord=11;col1="  不存在";output;
run;

*** Big N ***;
proc sql noprint;
	select sum(trt01pn=1) ,sum(trt01pn=2) into :pop1 trimmed,:pop2 trimmed
	from adam.adsl 
	where ittfl="Y";
quit;

%put _user_;

data adrs;
	merge adam.adrs(in=a) adam.adsl(keep=usubjid erfstag metnum smokstt egfrfl1 egfrfl2 tmmul linnum livemet);
	by usubjid;
	if a and( ittfl='Y' and  parqual="INDEPENDENT ASSESSOR"  and paramcd="BOR"
		and not missing(trt01pn)) and aval ne 6;
		/*与前table保持一致，排除NA的数据。*/
	if trt01pn=1 then trt01pn=3; /*差异有方向性*/
	if missing(crit1fl) then crit1fl="N"; 
	length agegr1_ sex_ erfstag_ metnum_ bralomet_ smokstt_ ecogbl_ egfr1 egfr2 tmmul_ linnum_ livemet_ $200;

	if not missing(agegr1) then agegr1_=cats(agegr1)||"岁";
	if not missing(sex) then sex_=ifc(sex="F","女性","男性");
	if find(erfstag,"IV") then erfstag_="IV";
		else erfstag_=erfstag;
	if erfstag_ not in ("IIIB","IIIC","IV") then erfstag_="";

	if metnum in ("1","2") then metnum_="<3";
		else if metnum in("3",">3") then metnum_="≥3";
	if bralomet="Y" then bralomet_="存在";
		else bralomet_="不存在";
	if smokstt in ("目前吸烟","曾经吸烟") then smokstt_="有";
		else if
{ #missing}
(smokstt) and smokstt="从未吸烟" then smokstt_="无";
	if ecogbl in ("0","1") then ecogbl_=ecogbl;
	if egfrfl1="Y" then egfr1="19Del";
	if egfrfl2="Y" then egfr2="L858R";
	if tmmul not in ("未知","") then tmmul_=tmmul;
	if linnum ne "" then linnum_=linnum;
	if livemet="Y" then livemet_="存在";
		else livemet_="不存在";

run;

%macro strata(part=1,stra=agegr1_);
proc sql noprint;
	create table ntot as
	select trt01pn,&stra.,count(distinct usubjid) as ntot
	from adrs 
	where not missing(&stra.)
	group by trt01pn,&stra.;
quit;


proc sql;
	create table adttee2 as
	select 1 as part,trt01pn,&stra.,count(distinct usubjid) as subjnum
	from adrs
	where crit1fl='Y' and  not missing(&stra.)
	group by part,trt01pn,&stra.
	order by trt01pn,&stra.;
quit;

data adttee2_;
	merge adttee2 ntot;
	by trt01pn &stra.;
	length val $200;
	if cmiss(subjnum,ntot)=0 then val=cats(subjnum,"/",ntot);
	else if missing(subjnum) then val=cats("0","/",ntot);
	else if cmiss(subjnum,ntot)=2 then val=cats("0","/","0");
	part=&part.;
	proc sort;by part &stra.;
run;

proc transpose data=adttee2_ out=adttee3 prefix=_col;
	by part &stra.;
	id trt01pn;
	var val;
run;

proc sort data=adrs out=adttee_use;by &stra.;run;

ods select none;
proc freq data=adttee_use;
	where cmiss(trt01pn,crit1fl)=0; /*crit1fl表示ORR，2x2，需都非空*/
	by &stra.;
	tables  %if &part ne 5 and &part ne 10 %then thrand*bmrand*;
			%if &part=5 %then thrand*;
			%if &part=10 %then bmrand*;trt01pn*crit1fl/cmh  all   binomial(level='Y') commonriskdiff(cl=score);
/*	ods output cmh=cmh(where=(althypothesis="非零相关")) commonpdiff=compdif(where=(method="Mantel-Haenszel"));不需要输出p值，图中不需要展示p值*/
	ods output commonpdiff=est;
quit;
ods select all;


proc sort data=adttee3;by &stra.;run;
data strata1_&stra.;
	length col1-col4 $200;
	merge adttee3 est(keep=&stra. value lowercl uppercl);
	by &stra.;
	if not missing(&stra.) then ord=input(&stra.,ord.);
	if not missing(value) then hr=cats(put(round(value*100,0.1),10.1));
		else hr="NE";
	if not missing(uppercl) then hrh=cats(put(round(uppercl*100,0.1),10.1));
		else hrh="NE";
	if not missing(lowercl) then hrl=cats(put(round(lowercl*100,0.1),10.1));
		else hrl="NE";
	col1=&stra.;
	if missing(_col2) then _col2="0/0";
	if missing(_col3) then _col3="0/0";
	col2=_col3;
	col3=_col2;
	col4=cats(hr)||" ("||catx(", ",hrl,hrh)||")";
	proc sort;by ord;
	

run;
%mend;

%strata(part=1,stra=agegr1_);
%strata(part=2,stra=sex_);
%strata(part=3,stra=erfstag_);
%strata(part=4,stra=metnum_);
%strata(part=5,stra=bralomet_);
%strata(part=6,stra=smokstt_); /**/
%strata(part=7,stra=ecogbl_);
%strata(part=8,stra=egfr1);
%strata(part=8,stra=egfr2);
%strata(part=9,stra=tmmul_);

%strata(part=10,stra=linnum_);
%strata(part=11,stra=livemet_);

data all;
	set strata1:;
	proc sort;by part ord;
run;

data final;
	merge all dummy(in=a) ;
	by part ord;
	if a;
	array cl col2-col3;
	do i=1 to 2;
		if ord ne . and missing(cl[i]) then cl[i]="0/0";  
	end; 
	if ord ne . and missing(col4) then col4="NE (NE, NE)";  
	
	order2=34-_n_+1;/*标记放置位置，y，注意是倒序，图呈现顺序和table不一致*/
	constant=0;/*标记放置位置，x*/
	proc sort;by order2;
run;
```

[[../../../03 STAT/项目中的/生存分析/05 生存分析-分层CMH计算ORR&DCR差值CI及p.md\|CMH客观缓解率统计方法参考这部分]]

---

# 3. 绘图

数据集内容如下：
![../../../Z appendix/Pasted image 20221217120955.png](/img/user/Z%20appendix/Pasted%20image%2020221217120955.png)


## 1. 绘图的sas代码-HR的森林图为例

```sas

/*Plots*/;
proc template;
    define statgraph forestplot;
        begingraph /*/ designwidth=1100px designheight=500px */;
	        dynamic min_yaxis max_yaxis;
	        layout lattice / columns=5 columngutter=0 columnweights=(0.20 0.16 0.16 0.28 0.18) rowdatarange=union;

             /*--Column # 1 --*/
	            layout overlay / walldisplay=none border=false
                             xaxisopts=(display=none offsetmin=0 offsetmax=1)
                             yaxisopts=(display=none tickvalueattrs=(size=8) linearopts=(tickvaluesequence=(start=1 
	                             end=34 increment=1)));
	                layout gridded / columns=1 location=outside halign=right valign=top;
                        entry halign=right textattrs=(size=8) "亚组" ;
                    endlayout;

	                scatterplot y=order2 x=constant /markercharacter=col1 markercharacterattrs=(size=8) 
		                markercharacterposition=right;
	            endlayout;

             /*--Column # 2 --*/
	            layout overlay / walldisplay=none border=false
                             xaxisopts=(display=none offsetmin=0 offsetmax=0)
                             yaxisopts=(display=none linearopts=(tickvaluesequence=(start=1 end=34 increment=1)));

	                entry TEXTATTRS=(size=8) "(进展或死亡人数/总人数)"  / location=outside valign=top;
	                entry TEXTATTRS=(size=8) "AK112+化疗"  / location=outside valign=top;
	                scatterplot y=order2 x=constant /markercharacter=col2 markercharacterattrs=(size=8)  ;
	             endlayout;

             /*--Column # 3 --*/
	            layout overlay / walldisplay=none border=false
                             xaxisopts=(display=none offsetmin=0 offsetmax=0)
                             yaxisopts=(display=none linearopts=(tickvaluesequence=(start=1 end=34 increment=1)));

	                entry TEXTATTRS=(size=8) '(进展或死亡人数/总人数)'/ location=outside valign=top;
	                entry TEXTATTRS=(size=8) '安慰剂+化疗'/ location=outside valign=top;
	                scatterplot y=order2 x=constant /markercharacter=col3 markercharacterattrs=(size=8)  ;
	             endlayout;

             /*--Column # 4 --*/
	            layout overlay / walldisplay=none border=false
                              xaxisopts=(display=(TICKVALUES LINE TICKS label) 
		                          labelattrs=(size=8 weight=normal) label='安慰剂Better      AK112Better' /*指定轴标签*/
	                              offsetmin=0 offsetmax=0 tickvalueattrs=(size=8)
	                              linearopts=(tickvaluelist=(0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17) 
	                              viewmin=-1 viewmax=18))/**线性指定刻度**/

                             yaxisopts=(display=none linearopts=(tickvaluesequence=(start=1 end=34 increment=1)));
     
	                scatterplot y=order2  x=HazardRatio /xerrorupper=HRUpperCL xerrorlower=HRLowerCL
                                                   markerattrs=graphdata1(symbol=circlefilled color=black size=8) ;
	                referenceline x=1 / lineattrs=(pattern=solid color=red);
	            endlayout;

             /*--Column # 5 --*/
	            layout overlay / walldisplay=none border=false
                             xaxisopts=(display=none offsetmin=0 offsetmax=0)
                             yaxisopts=(display=none linearopts=(tickvaluesequence=(start=1 end=34 increment=1)));
	                 entry TEXTATTRS=(size=8) 'HR(95% CI)'  / location=outside valign=top;
	                 scatterplot y=order2 x=constant /markercharacter=col4 markercharacterattrs=(size=8)  ;
	            endlayout;

            endlayout;
        endgraph;
    end;
run;

ods graphics /reset noborder  imagefmt=jpg noscale ;
ods html close;
options orientation=landscape;
ods graphics /reset noborder width=850px height=500px imagefmt=jpg noscale;
ods escapechar="@";
%rtfinit(pgmname=f-forest,pgmid=1,outdir=&_rootpath.\user\cuifan,style=rtftable,bodytitle=Y,orientation=landscape);

	proc sgrender data=final template=forestplot;
	run;

ods _all_ close;
ods listing;

%rtfpost(pgmname=f-forest,pgmid=1,outdir=&_rootpath.\user\cuifan);

```

## 2. 绘图的核心sas代码

> [!note]+ 绘图的核心sas代码
> - 先用layout lattice，规定总体布局为四列（一行），指定语句：
> 	- *layout lattice / columns=5 columngutter=0 columnweights=(0.20 0.16 0.16 0.28 0.18) rowdatarange=union;*
> - ---
> - 第一行title，有用各自layout布局里的entry语句的，也有嵌套Layout gridded语句的
> 	- layout gridded / columns=1 location=outside halign=right 
> 		- entry halign=right textattrs=(size=8) "亚组" ;
> 	- endlayout
> 	- 
> 	- 或者直接在layout overlay里写entry，写两个，后面写的在上面，有换行效果
> 		- entry TEXTATTRS=(size=8) '(进展或死亡人数/总人数)'/ location=outside valign=top;
> 		- entry TEXTATTRS=(size=8) '安慰剂+化疗'/ location=outside valign=top;
> - --
> - `各列主要用scatterplot语句做的，显示各列内容，用order2 constant定位，order2表示纵向分布，因为各column都只有一列，因此x恒定为0`
> 	- Column1:  *scatterplot y=order2 x=constant /markercharacter=`col1` markercharacterattrs=(size=8)  markercharacterposition=right;*
> 	- Column2:  *scatterplot y=order2 x=constant /markercharacter=`col2` markercharacterattrs=(size=8)  ;*
> 	- Column3:  *scatterplot y=order2 x=constant /markercharacter=`col3` markercharacterattrs=(size=8)  ;*
> 	- Column4:  *scatterplot y=order2  `x=HazardRatio /xerrorupper=HRUpperCL xerrorlower=HRLowerCL` markerattrs=graphdata1(symbol=circlefilled color=black size=8) ;*
> 	- Column5:  *scatterplot y=order2 x=constant /markercharacter=`col4` markercharacterattrs=(size=8)  ;*
> - ---
> - 在Column4，散点图加了个辅助线，用的refernce语句
> 	- referenceline x=1 / lineattrs=(pattern=solid color=red);


