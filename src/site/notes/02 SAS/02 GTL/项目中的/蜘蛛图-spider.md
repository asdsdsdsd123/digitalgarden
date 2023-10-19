---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/蜘蛛图-spider/","dgPassFrontmatter":true}
---


# 1. 输出结果

> [!note]+ 
>  ![../../../Z appendix/Pasted image 20230118230129.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230118230129.png)  
>  ![../../../Z appendix/Pasted image 20230118230343.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230118230343.png)

---

# 2. 产生final数据

## 1. 产生final数据-sas代码：

```sas
**----------------------------------------------------------------------------**;
**  Start Programming
**----------------------------------------------------------------------------**;
proc format;
	invalue parqualn
	    'INDEPENDENT ASSESSOR'=1
	    'INVESTIGATOR'=2
;
run;

data adrs;
    set adam.adrs;
	where ittfl="Y" and not find(upcase(trt01p),"PLACEBO");
run;

data adtr;
    set adam.adtr;
	where ittfl="Y" and paramcd="SUMDIAM" and trgrpid='TARGET' and trstat='' and not find(upcase(trt01p),"PLACEBO");
run;

data adsl;
    set adam.adsl;
	where ittfl="Y" and not find(upcase(trt01p),"PLACEBO");
    if eotstt='ONGOING' then trtongofl='Y';
    keep subjid trtongofl;
run;

proc sort data=adrs out=ovr;
    where paramcd='OVRLRESP';
    by parqual subjid visitnum;
run;

proc sort data=adtr out=diam;
    by parqual subjid visitnum;
run;

proc sort data=adrs out=bor(rename=(avalc=bor));  /*用于出图例，这个trt页，有BOR=CR/PR...的有几人*/
    where paramcd='BOR' ;
    by parqual subjid visitnum;
run;

data ovrsp_diam;/*选出对应visit的RS总评不是NE的TR记录*/
    merge ovr(keep=parqual usubjid subjid visitnum  aval avalc in=a)
          diam(keep=parqual usubjid subjid visitnum  ady pchg ablfl in=b);
    by parqual subjid visitnum ;
    length ovr $2;
    if ablfl="Y" then do;
        pchg=0;
        ady=0;
    end;
    ovr=strip(compress(avalc,,"ku")); /*保留大写字母*/
    if not missing(ady) then week=ady/7;
	if ovr ne "NE";
run;

proc sort;by subjid;run;
data ovrsp_diam2;
	merge adsl ovrsp_diam(in=a);
	by subjid;
	if a;
run;
proc sort;by parqual subjid;run;

data ovrsp_diam_bor;
    merge ovrsp_diam2 (in=a)/*ovrsp+diam*/
          bor(in=b keep=parqual usubjid subjid bor where=(bor ne 'NA'));
    by parqual subjid;
    length datalbel $10;
    array _ovr(*) pchg_cr pchg_pr pchg_sd pchg_pd pchg_ne;
    array _ovrc(5) $30  _temporary_ ("CR" "PR" "SD" "PD" "NE");
    do i=1 to 5;
        if bor=_ovrc(i) then do;
            _ovr(i)=pchg;/*例如前5行一个受试者，该受试者bor一个值，当前oversp=BOR的，另pchg_bor=当前pchg*/
        end;
    end;
    if last.subjid and trtongofl='Y' and missing(ablfl) then do;
        if not missing(week) then do;
			ady=ady+2;
            trtweek=ady/7;
            datalbel='→';/*每个线段最后加上治疗进行中的符号*/
        end;
    end;
    else call missing(trtweek,datalbel);/*不是Ongoing最后不用加→*/
    if a or b;
run;
proc sort;by parqual subjid week;run;

data  final;
	length sub $200;
    set ovrsp_diam_bor;
    by parqual subjid week;
	parqualn=input(parqual,parqualn.);
	if first.parqual then n=0;
	n+1;
	fx=10000000000;
	fy=10000000000;
	grp=ceil(n/150); /*一页放150观测*/
	if mod(input(subjid,??best.),2)=0 then sub="偶数";
		else sub="奇数";
/*	grp=1;   */
	keep parqual parqualn fx fy bor grp sub usubjid subjid week pchg ovr pchg_: trtweek datalbel;
run;
	proc sort;by parqualn usubjid week ;run;

```

## 2. 输出的数据集：

![../../../Z appendix/Pasted image 20230118232923.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230118232923.png)

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
**计算最大grp;
	select max(grp) into: maxgrp&i. trimmed
	from final
	where parqualn=&i.;
quit;

%do j=1 %to &&maxgrp&i;

	proc sql noprint;
	**计算该页各方面的人数;                                    /*为放图例，纵坐标范围大些*/
		select ceil(max(week)/6)*6, floor(min(pchg)/20)*20, ceil((max(pchg)+20)/20)*20
			into: maxx&i.&j. trimmed, : miny&i.&j. trimmed, : maxy&i.&j. trimmed
		from final
		where parqualn=&i. and grp=&j.;

		/*select count(distinct usubjid) into: cr&i.&j. trimmed from final where parqualn=&i. and grp=&j. and bor="CR";
		select count(distinct usubjid) into: pr&i.&j. trimmed from final where parqualn=&i. and grp=&j. and bor="PR";
		select count(distinct usubjid) into: sd&i.&j. trimmed from final where parqualn=&i. and grp=&j. and bor="SD";
		select count(distinct usubjid) into: pd&i.&j. trimmed from final where parqualn=&i. and grp=&j. and bor="PD";*/
		
		select distinct cats('"',subjid,'"') into :subjidgr1_&i.&j. separated by " "
		from final where sub="偶数" and parqualn=&i. and grp=&j.;
		select distinct  cats('"',subjid,'"') into :subjidgr2_&i.&j. separated by " "
		from final where sub="奇数" and parqualn=&i. and grp=&j.;
	quit;
	%put _user_;

    proc template;
        define statgraph spider&i.&j.;
            begingraph/ border=false designheight=6in designwidth=9in ;
			/*用于曲线，var=subjid*/
            discreteattrmap name='symbol'/ignorecase=true discretelegendentrypolicy=attrmap;
                %if %symexist(subjidgr1_&i.&j.) %then value &&subjidgr1_&i.&j./ markerattrs=(size=6 symbol=CircleFilled color=orange) lineattrs (color=orange);;
                %if %symexist(subjidgr2_&i.&j.) %then value &&subjidgr2_&i.&j. / markerattrs=(size=6 symbol=CircleFilled color=Red) lineattrs=(color=red);;
            enddiscreteattrmap;
            discreteattrvar attrvar=groupmark var=subjid attrmap='symbol';
            
			/*用于图例，var=sub*/
            discreteattrmap name='symbolsub'/ignorecase=true discretelegendentrypolicy=attrmap;
                %if %symexist(subjidgr1_&i.&j.) %then value "奇数" / markerattrs=(size=6 symbol=CircleFilled color=orange) lineattrs=(color=orange pattern=solid) ;;
                %if %symexist(subjidgr2_&i.&j.) %then value "偶数" / markerattrs=(size=6 symbol=CircleFilled color=Red) lineattrs=(color=red pattern=solid) ;;
            enddiscreteattrmap;
            discreteattrvar attrvar=groupmarksub var=sub attrmap='symbolsub';


            layout overlay /xaxisopts=(griddisplay=off  label='时间（周）'
                                    tickvalueattrs=(size=8) offsetmin=0 
                                    linearopts=(viewmin=0 viewmax=%eval(&&maxx&i.&j. + 1)
                                    tickvaluelist=(%do m=0 %to &&maxx&i.&j. %by 6; &m %end;)
                                    tickdisplaylist=('0' %do m=6 %to &&maxx&i.&j. %by 6; "&m." %end;))
                                    display=(tickvalues ticks line label)) 
                            yaxisopts=(label='靶病灶肿瘤负荷相对于基线变化百分比 (%)' type=linear labelattrs=(size=10) tickvalueattrs=(size=8)
                                    linearopts=(viewmin=&&miny&i.&j. viewmax=&&maxy&i.&j.
                                             tickvaluesequence=(start=&&miny&i.&j. end=&&maxy&i.&j. increment=20)));

/*画图上的曲线, group=subjid。曲线颜色根据groupmark的属性决定*/
                    seriesplot y=pchg x=week/group=groupmark grouporder=data  lineattrs=(thickness=1 pattern=solid) arkerattrs=(size=4 symbol=CircleFilled) break=true datalabelposition=right; 

/*                    referenceline x=0/lineattrs=(color=black) ; */

/*画图上的散点,group=subjid。曲线颜色根据groupmark的属性决定*/
                    scatterplot x=week y=pchg /group=groupmark grouporder=data errorbarattrs=(thickness=1.5)  markerattrs=(size=4 symbol=CircleFilled); 

/*画图上的Ongoing→,group=subjid。曲线颜色根据groupmark的属性决定*/
                    scatterplot x=trtweek y=pchg/group=groupmark grouporder=data /*Ongoing→*/   errorbarattrs=(thickness=1.5) markerattrs=(size=4 color=black) markercharacter=datalbel; 

                    referenceline y=20  / lineattrs=(pattern=dash color=black) curvelabel='+20%';
                    referenceline y=-30 / lineattrs=(pattern=dash color=black) curvelabel='-30%'; 
                    referenceline y= 0  / lineattrs=(pattern=solid color=black) curvelabel='0';

/*以下画图例，group=sub（分组为奇数 偶数）。显示点 线的图例，点线分别做了图例，最后用于layout grided的mergedlegend里*/
					scatterplot x=fx y=fy/group=groupmarksub name="sub"; 
					seriesplot x=fx y=fy/group=groupmarksub name="sub2";

/*最后一起做个大图例*/
                layout gridded / rows=1 order=rowmajor border=true halign=right valign=top location=inside;
                    mergedlegend "sub" "sub2"/ across=1 valueattrs=(size=8)  location=outside border=false halign=right valign=top;
                    entry halign=left textattrs=(size=8) " ";
                    entry halign=left textattrs=(size=8) "→ 仍在治疗";
                endlayout;

             endlayout;

           endgraph;
        end;
    run;
	data final2;
		set final;
		where parqualn=&i. and grp=&j.;
	run;

	proc sort;by parqualn parqual subjid week;run;

	proc sgrender data=final2 template=spider&i.&j.;
		by parqualn parqual;
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

**绘图的核心sas代码**

- `1.discreteattrmap每页定制相同属性，不根据数据改变：`  

discreteattrmap <mark style="background: #FFB8EBA6;">name='symbol'</mark>/ignorecase=true discretelegendentrypolicy=attrmap;  
	%if `%symexist(subjidgr1_&i.&j.)` %then **value &&subjidgr1_&i.&j.** / markerattrs=(size=6  symbol=CircleFilled color=orange) lineattrs=(color=orange);;  
	%if `%symexist(subjidgr2_&i.&j.)` %then value &&subjidgr2_&i.&j. / markerattrs=(size=6 symbol=CircleFilled color=Red) lineattrs=(color=red);;  
enddiscreteattrmap;    
discreteattrvar attrvar=<mark style="background: #FF5582A6;">groupmark</mark> **var=subjid** <mark style="background: #FFB8EBA6;">attrmap='symbol'</mark>;    

discreteattrmap name='symbolsub'/ignorecase=true discretelegendentrypolicy=attrmap;   
	%if %symexist(subjidgr1_&i.&j.) %then value "奇数" / markerattrs=(size=6 symbol=CircleFilled color=orange) lineattrs=(color=orange pattern=solid) ;;  
	%if %symexist(subjidgr2_&i.&j.) %then value "偶数" / markerattrs=(size=6 symbol=CircleFilled color=Red) lineattrs=(color=red pattern=solid) ;;  
enddiscreteattrmap;  
discreteattrvar attrvar=<mark style="background: #FF5582A6;">groupmarksub</mark> var=sub attrmap='symbolsub';  

> 
> ***使用1：groupmark***
>  - seriesplot y=pchg x=week/<mark style="background: #FF5582A6;">group=groupmark</mark> grouporder=data  lineattrs=(thickness=1 pattern=solid)   markerattrs=(size=4 symbol=CircleFilled) break=true datalabelposition=right ;
>     <font color="#ff0000"><u>按照groupmark指定的var=subjid分组显示，下同</u></font>
>  - scatterplot x=week y=pchg /<mark style="background: #FF5582A6;">group=groupmark</mark> grouporder=data errorbarattrs=(thickness=1.5)  markerattrs=(size=4 symbol=CircleFilled);
>  - scatterplot x=trtweek y=pchg/<mark style="background: #FF5582A6;">group=groupmark</mark> grouporder=data errorbarattrs=(thickness=1.5) markerattrs=(size=4 color=black) markercharacter=datalbel;
> ***使用2：groupmarksub***
> - scatterplot x=fx y=fy/<mark style="background: #FF5582A6;">group=groupmarksub</mark> `name="sub"`;
> - seriesplot x=fx y=fy/<mark style="background: #FF5582A6;">group=groupmarksub</mark> `name="sub2"`;
>     <u><font color="#ff0000">按照groupmarksub制定图例，分组是var=sub(subjid=奇数 偶数)，如果直接用groupmark作图例，会每个var=subjid的不同subjid值，图例显示一次，颜色变化一次</font></u>


- `2.x y根据宏变量设定坐标轴`	 

> - 线性轴：
>     x(y)axisopts=(griddisplay=off  label='时间（周）' tickvalueattrs=(size=8) offsetmin=0  
>         <font color="#ff0000">linearopts=(viewmin=0 viewmax=%eval(&&maxx&i.&j. + 1) </font>
>             <u><font color="#ff0000">tickvaluelist=(%do m=0 %to &&maxx&i.&j. %by 6; &m %end;) </font></u>
>             <u><font color="#ff0000">tickdisplaylist=('0' %do m=6 %to &&maxx&i.&j. %by 6; "&m." %end;)) </font></u>
>    display=(tickvalues ticks line label)) 
>   

`3.整合成大图例：`
> 	layout gridded / rows=1 order=rowmajor border=true halign=right valign=top location=inside;  
> 	    <font color="#ff0000">mergedlegend "sub" "sub2"</font>/ across=1 valueattrs=(size=8)  location=outside border=false 
> 	        halign=right valign=top; entry halign=left textattrs=(size=8) " "; 
> 	    entry halign=left textattrs=(size=8) "→ 仍在治疗";
> 	endlayout;

