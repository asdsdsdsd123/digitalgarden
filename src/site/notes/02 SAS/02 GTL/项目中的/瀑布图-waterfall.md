---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/瀑布图-waterfall/","dgPassFrontmatter":true}
---


# 1. 输出结果

 ![../../../Z appendix/Pasted image 20230117085608.png](/img/user/Z%20appendix/Pasted%20image%2020230117085608.png)
 ![../../../Z appendix/Pasted image 20230117085508.png](/img/user/Z%20appendix/Pasted%20image%2020230117085508.png)

---

# 2. 产生final数据集

## 1. 产生final数据集-sas代码：

```sas
proc format;
	invalue parqualn
	    'INDEPENDENT ASSESSOR'=1
	    'INVESTIGATOR'=2
;
run;

data adrs;
	set adam.adrs;
	where ittfl="Y" and paramcd='BOR' and not find(upcase(trt01p),"PLACEBO");
	length bor $2 ;
	bor=avalc;
run;

*---------------------------------------------------------------------------------*;
*                               Step 01: adtr adresp;                       
*---------------------------------------------------------------------------------*;
data adtr1;
    set adam.adtr;
    where ittfl="Y" and  paramcd='SUMDIAM' and not find(upcase(trt01p),"PLACEBO");
    if pchg ne .;
run;
proc sort;by parqual usubjid;run;

data adeff; /*每人首次PD日期及新抗中的最早日期，后面只展示PDDAT前及NEWADT前最小PCHG*/
	set adam.adrs;
	where fspdfl='Y' and not find(upcase(trt01p),"PLACEBO");
	adt_pd=min(adt,newatdt);
	keep parqual usubjid adt_pd;
	format adt_pd yymmdd10.;
run;
proc sort;by parqual usubjid;run;

data adtr2;/*删除TR中，PD后的靶病灶总经和数据*/
	merge adtr1(in=a) adeff;
	by parqual usubjid;
	if a;
	if adt>adt_pd>. then delete;
run;

proc sort data=adtr2;
    by parqual usubjid pchg;
run;

data adtr3; /*取PD前，每个parqual每个人靶病灶最小的pchg*/
    set adtr2;
    by parqual usubjid pchg;
    if first.usubjid;
run;

proc sql;
	create table adtr_rs as
		select b.*, a.pchg
			from adtr3 as a
			inner join adrs as b /*有部分只有RS，没有Tr数据，建议这里inner join,之前right join*/
			on a.usubjid=b.usubjid and a.parqual=b.parqual
;
quit;

data adtr_trt;
    set adtr_rs;
	parqualn=input(parqual,parqualn.);

/*    %trt;*/
run;

proc sort data=adtr_trt;
    by parqualn trt01pn descending pchg;
run;

data final;
	set adtr_trt;
	where bor ne "NA";
	by parqualn trt01pn descending pchg;
	if first.parqualn then n=0;
	n+1;
/*	grp=ceil(n/100);*/ /*每页每人放100个受试者*/
	grp=1;
		
/*	if first.trt01pn then ynum=1;*/
/*	else ynum+1;*/
	if pchg=0 and bor ne 'NA' then do;
        n_0=n;
        n_pchg=pchg+6; /*pchg=0+6*/
    end;

/*	if missing(pchg) and bor ne 'NA' then do; /*对于RS有总评结果，但是没有对应TR数据？*/*/
/*        n_0=n;*/
/*        n_pchg=6; /*pchg=.+6*/*/
/*    end;*/;


	if not missing(pchg) then do;
		if pchg>0 then pchg_bor_marker=pchg+4;
		if pchg<=0 then pchg_bor_marker=pchg-4;
	end; 

/*	trt=strip(put(trtn,trt.));*/
run;
```

## 2. 输出的数据集：

![../../../Z appendix/Pasted image 20230117091224.png](/img/user/Z%20appendix/Pasted%20image%2020230117091224.png)

---

# 3. 绘图

## 1. 绘图的sas代码

```sas

proc sql noprint;
**计算最大grp;
	select max(parqualn) into: maxpar trimmed
	from final;
quit;

%put &maxpar;

*---------------------------------------------------------------------------------*;
*                               Step 02: template;                       
*---------------------------------------------------------------------------------*;
option mprint;
%macro out;
%local i j;

ods graphics /reset noborder  imagefmt=jpg noscale ;

ods html close;
options orientation=landscape ps=160 ls=90;
ods graphics /reset noborder width=860px height=450px imagefmt=jpg noscale;
ods escapechar="@";
%rtfinit(pgmname=f-km,
        pgmid=1, 
        outdir=&_rootpath.\user\cuifan,
        style=rtftable,
        bodytitle=Y);

%do i=1 %to &maxpar.;

proc sql noprint;
**计算最大grp; /*grp，每个eval，放n个受试者，这个eval有几（grp）页，目前令grp=1*/
	select max(grp) into: maxgrp&i. trimmed
	from final
	where parqualn=&i.;
quit;

%do j=1 %to &&maxgrp&i;

	proc sql noprint;
	**计算该parqual 改页的总人数，NA PCHG=0 各个总评人数。用于后面灵活出图;
		select count(distinct usubjid) into: parna&i. trimmed 
		from adtr_trt
		where bor="NA" and parqualn=&i.; /*NA人数*/

		select count(distinct usubjid) into: pchg0&i.&j. trimmed 
		from final
		where parqualn=&i. and grp=&j.; /*PCHG=0人数*/

		select max(n) into: maxx&i.&j. trimmed /*这页受试者人数，x坐标轴相关*/
		from final
		where parqualn=&i. and grp=&j.;

		select count(distinct usubjid) into: cr&i.&j. trimmed 
			from final where parqualn=&i. and grp=&j. and bor="CR";
		select count(distinct usubjid) into: pr&i.&j. trimmed 
			from final where parqualn=&i. and grp=&j. and bor="PR";
		select count(distinct usubjid) into: sd&i.&j. trimmed 
			from final where parqualn=&i. and grp=&j. and bor="SD";
		select count(distinct usubjid) into: pd&i.&j. trimmed 
			from final where parqualn=&i. and grp=&j. and bor="PD";
		select count(distinct usubjid) into: ne&i.&j. trimmed 
			from final where parqualn=&i. and grp=&j. and bor="NE";
		
	quit;
	%put _user_;

    proc template;
        define statgraph waterfallplot&i.&j.;
            begingraph/ border=false designheight=6in designwidth=9in ;

            discreteattrmap name='symbolsbor'/ignorecase=true discretelegendentrypolicy=attrmap; 
            /*每页因总评结果个数不同，各个总评显示属性可能不同，在这里定制属性*/
                %if &&cr&i.&j. >0 %then value "CR" / markerattrs=(size=6 symbol=CircleFilled color=orange);;
                %if &&pr&i.&j. >0 %then value "PR" / markerattrs=(size=6 symbol=CircleFilled color=Red);;
                %if &&sd&i.&j. >0 %then value "SD" / markerattrs=(size=6 symbol=CircleFilled color=blue);;
                %if &&pd&i.&j. >0 %then value "PD" / markerattrs=(size=6 symbol=starFilled color=Green);;
                %if &&ne&i.&j. >0 %then value "NE" / markerattrs=(size=6 symbol=CircleFilled color=Gray);;
            enddiscreteattrmap;
            discreteattrvar attrvar=groupmarkbor var=bor attrmap='symbolsbor';


                layout overlay /xaxisopts = (label=" " griddisplay=off  display=(line)
                                    offsetmin=0.01 offsetmax=0.01  linearopts=(viewmax=&&maxx&i.&j. viewmin=1 
                                    tickvaluesequence=(start=1 end=&&maxx&i.&j. increment=1)))
                                    yaxisopts=(label='最佳百分比变化 (%)' type=linear 
                                    linearopts=(viewmin=-115 viewmax=100 tickvaluesequence=(start=-100 end=100 increment=20))
                                    );  

                        barchartparm x=n y=pchg/group=groupmark      /*条状图，①*/
                                                   name='d' orient=vertical 
                                                   display=(outline fill) 
                                                   barwidth=0.43 discreteoffset=0;

                        scatterplot x=n y=pchg_bor_marker/ group=groupmarkbor name='scatter2'  /*图上BOR标志，④*/ 
															markerattrs=(size=4)
                                                             errorbarattrs=(thickness=1.5) ;

						%if &&pchg0&i.&j. gt 0 %then %do;
                            scatterplot x=n_0 y=n_pchg/name='scatter1'
                                                                 markerattrs=(size=4 symbol=asterisk color=black)
                                                                 errorbarattrs=(thickness=1.5) ;/*②*/
                        %end;
                        referenceline y =20/lineattrs=(pattern = dash color = black) curvelabel='+20%';
                        referenceline y =-30/lineattrs=(pattern = dash color = black) curvelabel='-30%';

          		        layout gridded / rows=2 order=rowmajor border=false halign=left valign=bottom;
						%if &&pchg0&i.&j. gt 0 %then %do;
 							entry  halign=left '*：变化百分比为0%' / valign=bottom; /*②*/
						%end;
						%if &&parna&i. gt 0 %then %do;
					        entry  halign=left "&&parna&i.人无基线后评估" / valign=bottom; /*③*/
                        %end;
                       endlayout;

					layout gridded / rows=1 order=rowmajor border=false halign=right valign=bottom;
                        discretelegend "scatter2" / valueattrs=(size=8) location=inside autoalign=(bottomright) border=false across=5 pad=(bottom=0.5% right=8%);/*④*/
                    endlayout;

                endlayout;

            endgraph;
        end;
    run;

	proc sgrender data=final template=waterfallplot&i.&j.;
		by parqualn parqual;
		where parqualn=&i. and grp=&j.;
		title2 " ";
		title3 "#byval(parqual)";
    quit;

%end;
%end;

    ods _all_ close;
    ods listing;

%rtfpost(pgmname=f-km,pgmid=1,outdir=&_rootpath.\user\cuifan);

%mend;

%out;

```

## 2. 绘图的核心sas代码

> **绘图的核心sas代码**
> 
> - `绘制图形图：`
> 	barchartparm x=n y=pchg/group=groupmark  name='d' orient=vertical  
> 		display=(outline fill)  barwidth=0.43 discreteoffset=0;
> 
> - `discreteattrmap每页定制相同属性，不根据数据改变：`
> 	discreteattrmap name='symbolsbor'/ignorecase=true discretelegendentrypolicy=attrmap;
> 		%if &&cr&i.&j. >0 %then **value "CR"** / markerattrs=(size=6 symbol=CircleFilled color=orange);;
> 		%if &&pr&i.&j. >0 %then value "PR" / markerattrs=(size=6 symbol=CircleFilled color=Red);;
> 		%if &&sd&i.&j. >0 %then value "SD" / markerattrs=(size=6 symbol=CircleFilled color=blue);;
> 		%if &&pd&i.&j. >0 %then value "PD" / markerattrs=(size=6 symbol=starFilled color=Green);;
> 		%if &&ne&i.&j. >0 %then value "NE" / markerattrs=(size=6 symbol=CircleFilled color=Gray);;
> 		enddiscreteattrmap;
> 	discreteattrvar *attrvar=groupmarkbor* **var=bor** attrmap=`'symbolsbor'`;   /*groupmarkbor,属性以group展现*/
> 
> - `discreteattrmap定制的图应用到绘图语句上（如scatterplot）`	
>      scatterplot x=n y=pchg_bor_marker/ *group=groupmarkbor* ` name='scatter2'`    /*图上BOR标志*/ markerattrs=(size=4) errorbarattrs=(thickness=1.5) ;
>    
> - `scatterplot指定显示图点，x y指定位置，symbol指定显示符号`
>      scatterplot `x=n_0` `y=n_pchg`/arkerattrs=(size=4 `symbol=asterisk` color=black) ;
>    
> - `根据实际数据展示图例：`
> 	- <mark style="background: #FFF3A3A6;">左下角图例</mark>：
> 	   layout gridded / rows=2 order=rowmajor border=false halign=left valign=bottom;
> 		   %if &&pchg0&i.&j. gt 0 %then %do;
> 			   entry  halign=left '\*：变化百分比为0%' / valign=bottom;
> 			%end;
> 				
> 			%if &&parna&i. gt 0 %then %do;
> 				entry  halign=left "&&parna&i.""人无基线后评估" / valign=bottom;
> 			%end;
> 		endlayout;
>   
> 	- <mark style="background: #FFF3A3A6;">右下角图例</mark>：
> 	  layout gridded / rows=1 order=rowmajor border=false halign=right valign=bottom;
> 		  `discretelegend` "scatter2" / valueattrs=(size=8) location=inside autoalign=(bottomright) border=false across=5 pad=(bottom=0.5% right=8%);
> 	  endlayout;

