---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/泳道图-swim/","dgPassFrontmatter":true}
---


# 1. 输出结果

![../../../Z appendix/Pasted image 20230101110742.png](/img/user/Z%20appendix/Pasted%20image%2020230101110742.png)

---

# 2. 产生final数据

## 1. 产生final数据-sas代码：

```sas
data adrs;
    set adam.adrs;
	where ittfl="Y" and find(trt01p,"AK112");
run;

data adsl;
    set adam.adsl;
	where ittfl="Y" and find(trt01p,"AK112");
run;

proc sort data=adrs out=bor(keep=subjid parqual avalc);
    where paramcd="BOR" ;
    by subjid parqual;
run;

data adsl2;
    merge adsl(in=a) bor(rename=(avalc=bor));
    by subjid;
    if a;
	zero=0;
/*    bor=compress(scan(bor,3),,'ak');*/
	duration=(trtedt-trtsdt+1)/30.4375;
	length datalbel $200;

    if bor="NA" then do;/*：患者早期由于xxxx，无给药后疗效数据评价*/
		datalbel="*";
		x_duration1=duration+0.1;
	end;

    keep parqual subjid duration datalbel eotstt bor x_duration: zero;
run;

proc sort data=adsl2;
	by parqual subjid;
run;
   
proc sort data=adrs out=ovrl(keep=parqual subjid visitnum ady adt avalc ) nodupkey;
    where paramcd="OVRLRESP" and not missing(ady);
    by parqual subjid ady avalc ;
run;

data adrs_aval;
    set ovrl;
    if index(avalc,"(CR)") then avalcr=ady/30.4375;
    else if index(avalc,"(PR)") then avalpr=ady/30.4375;
    else if index(avalc,"(SD)") or index(upcase(avalc),"(NON-CR/NON-PD)") then do;
		avalsd=ady/30.4375;
		avalc="(SD)";
	end;
    else if index(avalc,"(PD)") then avalpd=ady/30.4375;
    else if index(avalc,"(NE)") then avalne=ady/30.4375;
	else if index(avalc,"(NA)") then avalna=ady/30.4375;
	max=ady/30.4375;	/*横坐标*/
run;

%macro tran(id=,n=);
proc transpose data=adrs_aval(where=(find(avalc,"(&id.)","i"))) out=adrs&n. prefix=aval&id.;
    by parqual subjid;
    var aval&id.;	   
run;

proc sql noprint;
	create table count&n. as 
    select distinct parqual, subjid, sum(not missing(ady)) as num from adrs_aval(where=(find(avalc,"(&id.)","i"))) 
		group by parqual, subjid ;	  
quit;	

%mend;

%tran(id=cr,n=1);
%tran(id=pr,n=2);
%tran(id=sd,n=3);
%tran(id=pd,n=4);
%tran(id=ne,n=5);
%tran(id=na,n=6);

data adrs_all;
    merge adrs1-adrs6;
    by parqual subjid;
	if not missing(subjid);
    drop _name_;
run;    
 
data final;
    merge adsl2(in=a)  adrs_all;             
    by parqual subjid;
    if a;
	if first.parqual then n=0;
	n+1;
	grp=ceil(n/20);
/*	trt=put(trtn,trt.);*/
run;

proc sort;by parqual subjid;run;

proc sql noprint;        
	select max(grp) into: maxgrp1- 
	from final	/*取最大grp*/		
	group by parqual;

	select 4*ceil(max(duration)/3) into: maxx1- 
	from final	/*取最大横坐标*/	
	where parqual="INDEPENDENT ASSESSOR"
	group by grp;

	select 4*ceil(max(duration)/3) into: maxx%sysevalf(&maxgrp1.+1)- 
	from final	/*取最大横坐标*/		
	where parqual ne "INDEPENDENT ASSESSOR"
	group by grp;

quit;

%put _user_;

data final;
	set final;
	if parqual ne "INDEPENDENT ASSESSOR" then grp=grp+&maxgrp1.;
run;
```

## 2. 输出的数据集：

![../../../Z appendix/Pasted image 20230101113900.png](/img/user/Z%20appendix/Pasted%20image%2020230101113900.png)

---

# 3. 绘图

## 1. 绘图的sas代码

```sas
*---------------------------------------------------------------------------------*;
*                               template;
*---------------------------------------------------------------------------------*;
    ods graphics /reset noborder  imagefmt=jpg noscale ;
     
    ods html close;
    options orientation=portrait;
    ods graphics /reset noborder width=800px height=440px imagefmt=jpg noscale;
    ods escapechar="@";
    %rtfinit(pgmname=f-swim,
              pgmid=1,
              outdir=&_rootpath.\user\cuifan,
              style=rtftable,
              bodytitle=Y);
	 %macro out;

	 %do grp=1 %to %sysevalf(&maxgrp1.+&maxgrp2.);

	data _null_;
		set final end=last;  /*20个受试者当成一页，grp=1/2/3...，在当前grp计算各疗效的变量缺失个数。若grp=1，CR相关变量全部缺失，则scatter CR点时会报错。*/
		where grp=&grp.;
		retain numcr numpr numpd numsd numne 0;
		crn=n(of avalcr1-avalcr4);
		prn=n(of avalpr1-avalpr5);
		pdn=n(of avalpd1-avalpd3);
		sdn=n(of avalsd1-avalsd5);
		nen=n(of avalne1-avalne1);

		if crn>numcr then numcr=crn;
		if prn>numpr then numpr=prn;
		if pdn>numpd then numpd=pdn;
		if sdn>numsd then numsd=sdn;
		if nen>numne then numne=nen;

		if last then do;
			call symputx("numcr",numcr); /*若为0，则表面当前grp CR疗效变量都为空的*/
			call symputx("numpr",numpr);
			call symputx("numpd",numpd);
			call symputx("numsd",numsd);
			call symputx("numne",numne);
		end;

	run;

     proc template;
         define statgraph swim&grp.;
             begingraph / designwidth=6.5in designheight=5in border=false;
            discreteattrmap name='symbols'/ignorecase=true discretelegendentrypolicy=attrmap;  /*(1)hightlowplot的属性，根据某变量不同值赋予不同属性*/
                value "ONGOING" / fillattrs=(color=cxafb4db) ;
                value "DISCONTINUED"  / fillattrs=(color=cxf7acbc) ;
/*                value "COMPLETED" / fillattrs=(color=cxafb4db) ;*/
            enddiscreteattrmap;
            discreteattrvar attrvar=groupmark var=eotstt attrmap='symbols';
			/*(3)定义图例样式。和前面类似，一个图语句固定一个属性*/
         	%if %sysevalf(&numcr. gt 0) %then legenditem type=marker name="d1" / markerattrs=(size=6 symbol=CircleFilled color=blue) label="CR";;
			%if %sysevalf(&numpr. gt 0) %then legenditem type=marker name="d2" / markerattrs=(size=6 symbol=CircleFilled color=green) label="PR";;
         	%if %sysevalf(&numsd. gt 0) %then legenditem type=marker name="d3" / markerattrs=(size=6 symbol=CircleFilled color=yellow) label="SD";;
			%if %sysevalf(&numpd. gt 0) %then legenditem type=marker name="d4" / markerattrs=(size=6 symbol=CircleFilled color=Red) label="PD";;
			%if %sysevalf(&numne. gt 0) %then legenditem type=marker name="d5" / markerattrs=(size=6 symbol=CircleFilled color=brown) label="NE";;

                    layout overlay /
                        yaxisopts=(offsetmin=0.02 offsetmax=0.02 type=discrete
                                   /*discreteopts=(viewmax=&maxyaxis viewmin=1 tickvalueformat=num. tickvaluesequence=(start=1 end=&maxyaxis increment=1))*/
                                   label="受试者" griddisplay=off /*tickvalueattrs=(size=8) labelattrs=(size=8)*/
                                   display=(line label ticks tickvalues))

                        xaxisopts=(offsetmin=0
                                   linearopts=(viewmax=&&maxx&grp. viewmin=-0.5 tickvaluesequence=(start=0 end=&&maxx&grp. increment=3))
                                   label='时间（月）' griddisplay=off
                                   display=(tickvalues ticks label line));

                        highlowplot y=subjid low=zero high=duration / display=(fill) type=bar barwidth=0.7 group=groupmark name='highlowplot' grouporder=ascending;
                        scatterplot y=subjid x=x_duration1 / markercharacterattrs=(size=10 color=black) markercharacter=datalbel; /*✳标记，对应于Mock Up中✳，图中(2)部分*/
						%if %sysevalf(&numcr. gt 0) %then %do;
						%do j=1 %to &numcr.;							
	                        scatterplot y=subjid x=avalcr&j. / markerattrs=(size=6 symbol=CircleFilled color=blue)
	                                errorbarattrs=(thickness=1.5) name="d1" legendlabel="CR";
						%end;
						%end;

						%if %sysevalf(&numpr. gt 0) %then %do;
						%do j=1 %to &numpr.;							
	                        scatterplot y=subjid x=avalpr&j. / markerattrs=(size=6 symbol=CircleFilled color=green)
	                                errorbarattrs=(thickness=1.5) name="d2" legendlabel="PR";
						%end;
						%end;

						%if %sysevalf(&numsd. gt 0) %then %do;
						%do j=1 %to &numsd.;
	                        scatterplot y=subjid x=avalsd&j. / markerattrs=(size=6 symbol=CircleFilled color=yellow)
	                                errorbarattrs=(thickness=1.5) name="d3" legendlabel="SD";
						%end;
						%end;

						%if %sysevalf(&numpd. gt 0) %then %do;
						%do j=1 %to &numpd.;
	                        scatterplot y=subjid x=avalpd&j. / markerattrs=(size=6 symbol=starFilled color=red)
	                                errorbarattrs=(thickness=1.5) name="d4" legendlabel="PD";							
						%end;
						%end;

						%if %sysevalf(&numne. gt 0) %then %do;
						%do j=1 %to &numne.;
	                        scatterplot y=subjid x=avalne&j. / markerattrs=(size=6 symbol=CircleFilled color=purple)
	                                errorbarattrs=(thickness=1.5) name="d5" legendlabel="NE";							
						%end;
						%end;

                        layout gridded / rows=1 order=rowmajor border=true autoalign=(right) outerpad=(top=20%); /*定制右边图例*/
							entry textattrs=(size=8) halign=left "总体疗效评价：";
                            discretelegend %if %sysevalf(&numcr. gt 0) %then "d1";
									%if %sysevalf(&numpr. gt 0) %then "d2";
									%if %sysevalf(&numsd. gt 0) %then "d3";
									%if %sysevalf(&numpd. gt 0) %then "d4";
									%if %sysevalf(&numne. gt 0) %then "d5"; / across=1 valueattrs=(size=8)  location=inside border=false halign=left valign=bottom;

							entry textattrs=(size=8) halign=left " ";
							entry textattrs=(size=8) halign=left "研究状态：";

                            discretelegend "highlowplot" / across=1 valueattrs=(size=8)  location=inside border=false halign=left valign=bottom;

                        endlayout;
         
                    endlayout;
                endgraph;
            end;
        run;


    proc sgrender data=final template=swim&grp.;
		by parqual;
		title3 "#byval1"; /*输出第三行显示parqual的值*/
		where grp=&grp.;
    quit;
%end;

%mend;

%out;
    ods _all_ close;
    ods listing;

    %rtfpost(pgmname=f-swim,
              pgmid=1,
              outdir=&_rootpath.\user\cuifan);

options mprint;

```

## 2. 绘图的核心sas代码

### 2.1. discreteattrmap和legenditem

> [!note]+ discreteattrmap
> - __discreteattrmap 根据指定变量的值，赋予给定的属性__
> ```ad-green
> - You can use a discrete attribute map to associate visual attributes with specific  group values, which enables you to make plots that are consistent regardless of the  data order。类似根据数据不同group自定义显示离散的样式(样式如各种ATTRS)，它使能够在不考虑数据顺序的情况下绘制一致的图
> ```
> - `discreteattrmap name='symbols'`/ignorecase=true discretelegendentrypolicy=attrmap;
> 	- *value "ONGOING"* / fillattrs=(color=cxafb4db) ;
> 	- *value "DISCONTINUED"*  / fillattrs=(color=cxf7acbc) ;
> - enddiscreteattrmap;
> - discreteattrvar `attrvar=groupmark` var=*eotstt*   attrmap=`'symbols'` ;
> - ---
>   ***使用：***;
> - highlowplot y=subjid low=zero high=duration / display=(fill) type=bar barwidth=0.7 `group=groupmark` `name='highlowplot'` grouporder=ascending; ***/\*group=groupmark，var=eotstt，按照这个分group组，展示不同颜色（由前discreteattmap指定\*/***
> - discretelegend `"highlowplot"` / across=1 valueattrs=(size=8)  location=inside border=false halign=left valign=bottom;

> [!note]+ legenditem
> - __legenditem对图例进行定制，是总评结果，因每人情况不同，因此用宏。先定义属性__
> ```ad-green
> - legenditem: You can use the LEGENDITEM statement to manually add items to your legend that  do not appear in your plot. This enables you to provide additional information about  your plot or to build a common legend that you can use with multiple plots.
> 	- LEGENDITEM TYPE=type NAME="string" </option(s)>;
> ```
> - %if %sysevalf(&numcr. gt 0) %then `legenditem type=marker name="d1" / markerattrs=(size=6 symbol=CircleFilled color=blue) label="CR";;`
> - %if %sysevalf(&numpr. gt 0) %then `legenditem type=marker name="d2" / markerattrs=(size=6 symbol=CircleFilled color=green) label="PR";;`
> - %if %sysevalf(&numsd. gt 0) %then `legenditem type=marker name="d3" / markerattrs=(size=6 symbol=CircleFilled color=yellow) label="SD";;`
> - %if %sysevalf(&numpd. gt 0) %then `legenditem type=marker name="d4" / markerattrs=(size=6 symbol=CircleFilled color=Red) label="PD";;`
> - %if %sysevalf(&numne. gt 0) %then `legenditem type=marker name="d5" / markerattrs=(size=6 symbol=CircleFilled color=brown) label="NE";;`
> 
> - ---
>   ***使用：***
> - %if %sysevalf(&numcr. gt 0) %then %do;***/\*画图，图上总评点属性用上legenditem定义的。以下d2-d5省略\*/***;
> 	- %do j=1 %to &numcr.;
> 		- scatterplot y=subjid x=`avalcr&j.` / markerattrs=(size=6 symbol=CircleFilled color=blue) errorbarattrs (thickness=1.5) `name="d1"` `legendlabel="CR"`; 
> 	- %end;
> - %end;
> 
> - discretelegend %if %sysevalf(&numcr. gt 0) %then `"d1"`; ***/\*将图例输出\*/***;
> 	- %if %sysevalf(&numpr. gt 0) %then "d2";
> 	- %if %sysevalf(&numsd. gt 0) %then "d3"; 
> 	- %if %sysevalf(&numpd. gt 0) %then "d4";
> 	- %if %sysevalf(&numne. gt 0) %then "d5"; / across=1 valueattrs=(size=8)  location=inside border=false halign=left valign=bottom;
> 

> [!note]+ reference discreteattrmap&legenditem
> - 注意这俩都需要写在最前，begingraph下的，不包含在任何LayOut中
> - [[obsidian://booknote?type=annotation&book=100 PDF/SAS9.4 GTL.pdf&id=6101593e-7a91-049e-85de-1774f19961b6&page=1313&rect=72,187.795,228.039,213.749\|Example Program]]
> - ![../../../Z appendix/Pasted image 20221219224212.png](/img/user/Z%20appendix/Pasted%20image%2020221219224212.png)

> [!note]+ 
> - __用scatterplot展示图标点__
> 	- scatterplot y=subjid x=x_duration1 / markercharacterattrs=(size=10 color=black) `markercharacter=datalbel`;  
> 		- ***/\*x y为坐标点。markercharacter=datalbel，指定显示图标为变量datalbel的变量值（如datalbel="\*"，显示坐标为\*）\*/***;
> 		- 其中datalbel为变量，变量值为"\*"

### 2.2. 整合成一个大图例

> [!note]+ 整合成一个大图例
> - layout gridded / rows=1 order=rowmajor border=true autoalign=(right) outerpad=(top=20%);
> 	- entry textattrs=(size=8) halign=left `"总体疗效评价："`;
> 	- discretelegend %if %sysevalf(&numcr. gt 0) %then `"d1"`;
> 		- %if %sysevalf(&numpr. gt 0) %then `"d2"`;
> 		- %if %sysevalf(&numsd. gt 0) %then `"d3"`;
> 		- %if %sysevalf(&numpd. gt 0) %then `"d4"`;
> 		- %if %sysevalf(&numne. gt 0) %then `"d5"`; / across=1 valueattrs=(size=8)  location=inside border=false  halign=left valign=bottom;
> 	- entry textattrs=(size=8) halign=left `" "`;
> 	- entry textattrs=(size=8) halign=left `"研究状态："`;
> 	- discretelegend `"highlowplot"` / across=1 valueattrs=(size=8)  location=inside border=false halign=left valign=bottom;
> - endlayout;
> - ---------------------------------------------------------------------------------
> - ![../../../Z appendix/Pasted image 20230101143306.png](/img/user/Z%20appendix/Pasted%20image%2020230101143306.png)





