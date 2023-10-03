---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/PK个体图（叠加改剂量组平均图）/","dgPassFrontmatter":true}
---


# 1. 输出结果

一般平均值用的ATPT，个体图用ARELTM（实际偏差时间）

![../../../Z appendix/Pasted image 20230125211123.png](/img/user/Z%20appendix/Pasted%20image%2020230125211123.png)

---

# 2. sas产生数据

## 1. sas代码：

```sas
proc sql;
	create table adpc as
	select a.*,b.aval as tmax
	from adam.adpc as a left join adam.adpp(where=(paramcd="TMAX")) as b
		on a.usubjid=b.usubjid;
quit;

data adpc2;
	length trt trt01a subjid $200;
	set adpc;
	where ^ missing(aval) and find(trt01a,"试验");

	trt=dsdole;

	if
{ #missing}
(atpt) then do;
		if find(atpt,"EOI") then do;
			if find(atpt,"后") then point=input(ksubstr(kscan(atpt,2,"后"),1,2),??best.);
				else if not find(atpt,"后") then point=0; /*atptn=2，刚给药的结果，横坐标那里记为0*/
		end;

		else if find(atpt,"静脉给药前") then point=0; /*atptn=1，给药前的结果，横坐标那里记为0*/
		else point=(input(kcompress(atpt,"D"),??best.)-1)*24;
	end;

	apt=point/24;

	if point>tmax and bqlfl="Y" then aval=.;
	if point<tmax and bqlfl="Y" then aval=0;
	if find(trt01a,"试验") then trtgrp="试验组";
/*		else if find(trt01a,"安慰") then trtgrp="安慰组";*/
run;

proc sql;
	create table trtpla_mean(rename=(aval_=aval)) as
	select trt01a,apt,mean(aval) as aval_
	from adpc2
	where
{ #missing}
(aval)
	group by trt01a,apt;
quit;

data final;
	set adpc2 trtpla_mean(in=b);
	if b then do;
		if find(trt01a,"试验") then subjid="试验组";
/*			else if find(trt01a,"安慰") then subjid="安慰组";*/
	end;
run;

proc sort data=final;
	by subjid apt;
run;

```

## 2. 输出的数据集：

![../../../Z appendix/Pasted image 20230125211635.png](/img/user/Z%20appendix/Pasted%20image%2020230125211635.png)

---

# 3. 绘图

## 1. 绘图的sas代码

```sas

proc template;
	define statgraph xx;
        dynamic trt01a randnum;
		begingraph/designwidth=10in designheight=5in;
		entrytitle "剂量组：" trt01a "，随机号：" randnum;
		entryfootnote "脚注";
			layout lattice/rows=1 columns=2;  
				cell;
					cellheader; /*每个部分定义cell，再cell中定义entry作为cell的标题，再对cell定义layout画其中的图*/
	                	entry "线性标度" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/walldisplay=all xaxisopts=(type=linear label="采集时间（天）" 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=0 viewmax=60
	                                    tickvaluesequence=(start=0 end=60 increment=10)))
									yaxisopts=(type=linear label=" " 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=31000 viewmax=62000
	                                    tickvaluesequence=(start=31000 end=62000 increment=3000)));
										drawtext textattrs=(size=10 family="Times New Roman") "CAR+"  /*可自定义制作横坐标轴，尤其是带特殊字符，写在label里不生效的*/
											textattrs=(size=10 family="宋体") "细胞数("
										    textattrs=(size=10 family="Times New Roman") "CAR+"
										    textattrs=(size=10 family="宋体") "细胞数"
										    textattrs=(size=10 family="Times New Roman") "/μL)"/ x=-10.5 y=50 anchor=bottom xspace=wallpercent yspace=wallpercent rotate=90 width=100 justify=center;

									seriesplot y=aval x=apt/group=subjid name="a";
									scatterplot y=aval x=apt/group=subjid name="b";
									mergedlegend "a" "b"/location=inside halign=right valign=top across=1;
					endlayout;
				endcell;

				cell;
					cellheader;
	                	entry "半对数标度" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/xaxisopts=(type=linear label=" " 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=0 viewmax=60
	                                    tickvaluesequence=(start=0 end=60 increment=10)))
									yaxisopts=(type=log label="人血清白蛋白 (μg/mL)" 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   logopts=(base=10 viewmin=31000 viewmax=61000
	                                    tickvaluelist=(31000 37000 43000 49000 55000 61000)));

									drawtext textattrs=(size=10 family="Times New Roman") "CAR+" /*可自定义制作纵坐标轴，尤其是带特殊字符，写在label里不生效的*/
											textattrs=(size=10 family="宋体") "细胞数("
										    textattrs=(size=10 family="Times New Roman") "CAR+"
										    textattrs=(size=10 family="宋体") "细胞数"
										    textattrs=(size=10 family="Times New Roman") "/μL)"/ x=50 y=-10.5 anchor=bottom xspace=wallpercent yspace=wallpercent rotate=0 width=100 justify=center;

									seriesplot y=aval x=apt/group=subjid name="a";
									scatterplot y=aval x=apt/group=subjid name="b";
									mergedlegend "a" "b"/location=inside halign=right valign=top across=1;
					endlayout;
				endcell;
				drawtext textattrs=(size=10 family="宋体") "剂量组 = " trt01a
					 /x=50 y=-3 anchor=bottom rotate=0 width=100 justify=center;
			endlayout;
		endgraph; 
	end;
run;

option mprint mlogic symbolgen;
%macro test;
proc sql noprint;
	select distinct subjid, trtgrp, trt01a, randnum
		into :subjids separated by "|",:trtgrps separated by "|",
			:trt01as separated by "|",:randnums separated by "|"
	from final
	where not find(subjid,"组") and pcstat="";
quit;

%put _user_;

ods listing close;
options orientation=landscape ps=160 ls=80;
ods escapechar="@";
ods graphics /reset noborder imagefmt=jpg noscale;
%rtfinit(pgmname=f-pc, pgmid=1, outdir=&_rootpath.\user\cuifan\PCPK,style=rtftable,bodytitle=Y);
%do i=1 %to %sysfunc(countw(&subjids,|));
	%let subjid=%scan(&subjids,&i,|);
	%let trtgrp=%scan(&trtgrps,&i,|);
	%let trt01a=%scan(&trt01as,&i,|);
	%let randnum=%scan(&randnums,&i,|);
	proc sgrender data=final template=xx;
		where
{ #missing}
(aval) and subjid in ("&subjid.","&trtgrp."/*,"安慰组"*/);
		dynamic randnum="&randnum." trt01a="&trt01a.";
	run;
%end;
ods _all_ close;
ods listing;

%rtfpost(pgmname=f-pc,pgmid=1,outdir=&_rootpath.\user\cuifan\PCPK);


%mend test;

%test

```

## 2. 绘图的核心sas代码
 
>
> `1.scatterplot+seriesplot绘制`
> ---
> `2.drawtext绘制横纵坐标`，主要是涉及unicode写在label里不显示之类
> 
> `绘制横坐标：`
> 	drawtext textattrs=(size=10 family="Times New Roman") "CAR+"  
> 		textattrs=(size=10 family="宋体") "细胞数("
> 		textattrs=(size=10 family="Times New Roman") "CAR+"
> 		textattrs=(size=10 family="宋体") "细胞数"
> 		textattrs=(size=10 family="Times New Roman") "/μL)"`/ `
> 			`x=-15 y=50 anchor=bottom xspace=wallpercent yspace=wallpercent rotate=90 width=100 justify=center`;
> 
> `绘制纵坐标：`
> 	drawtext textattrs=(size=10 family="Times New Roman") "CAR+"
> 		textattrs=(size=10 family="宋体") "细胞数("
> 		textattrs=(size=10 family="Times New Roman") "CAR+"
> 		textattrs=(size=10 family="宋体") "细胞数"
> 		textattrs=(size=10 family="Times New Roman") "/μL)"`/`
> 			`x=50 y=-15anchor=bottom xspace=wallpercent yspace=wallpercent rotate=0 width=100 justify=center`;
> `绘制脚注：`
> 	drawtext textattrs=(size=10 family="宋体") "剂量组 = " trt01a 
> 		/x=50 y=-3 anchor=bottom rotate=0 width=100 justify=center;  /*注意写的位置：在整个图的下边写文本框*/
> ---

```ad-note
collapse: open
title: 3.mergelegend与discretelegend

` mergelegend，两种图例组合到一起，否则discretelegend只显示一种的`

***mergedlegend*** "a" "b"/location=inside halign=right valign=top across=1;
 ![Pasted image 20230125212437.png](/img/user/Z%20appendix/Pasted%20image%2020230125212437.png)
 
 ***discretelegend*** "a" "b"/location=inside halign=right valign=top across=1;
![Pasted image 20230125212635.png](/img/user/Z%20appendix/Pasted%20image%2020230125212635.png)

```

