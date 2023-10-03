---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["002-SAS/02-GTL/项目中的"],"permalink":"/002-SAS/02-GTL/项目中的/PK散点图/","dgPassFrontmatter":true}
---


# 1. 输出结果

![../../../Z appendix/Pasted image 20230125214924.png](/img/user/Z%20appendix/Pasted%20image%2020230125214924.png)

---

# 2. sas产生数据

## 1. 产生数据-sas代码：

```sas
data adpp;
	set adam.adpp;
	where not find(trt01a,"安慰剂") and paramcd in ("AUCLST","CMAX");
	dose=input(compress(dsdole,,"kd"),??best.);
	if  aval>0 then y=log(aval);
	if dose>0 then x=log(dose);
run;

**相关性统计分析-auc; /*表格对应的方法，如proc mixed，一般用此*/
ods output PearsonCorr=corrauc;
proc corr data=adpp;
	var x;
	with y;
	where paramcd="AUCLST";
quit;

data corrauc;
	length variable $200;
	set corrauc;
	variable="AUCLST";
	r0=strip(put(x,8.3));
	if px=<0.0001 then p0="<0.0001";
	else p0=strip(put(px,8.4));
	call symput('aucr0',cats(r0));
	call symput('aucp0',cats(p0));
run;

**相关性统计分析-cmax; /*表格对应的方法，如proc mixed，一般用此*/
ods output PearsonCorr=corrcmax;
proc corr data=adpp; 
	var x;
	with y;
	where paramcd="CMAX";
quit;

data corrcmax;
	length variable $200;
	set corrcmax;
	variable="CMAX";
	r0=strip(put(x,8.3));
	if px=<0.0001 then p0="<0.0001";
	else p0=strip(put(px,8.4));
	call symput('cmaxr0',cats(r0));
	call symput('cmaxp0',cats(p0));
run;

proc sql;
	create table adpp2 as
	select a.*,b.r0 as aucr0,b.p0 as aucp0,c.r0 as cmaxr0,c.p0 as cmaxp0
	from adpp as a left join corrauc as b on a.paramcd=b.variable
		left join corrcmax as c on a.paramcd=c.variable;
quit;

**为出图水平转置;
proc sort data=adpp2;
	by usubjid x ;
run;

data final(drop=paramcd);
	retain usubjid x auclst aucr0 aucp0 cmax cmaxr0 cmaxp0;
	merge adpp2(keep=usubjid x y aucr0 aucp0 paramcd rename=(y=auclst) where=(paramcd="AUCLST"))
		adpp2(keep=usubjid x y cmaxr0 cmaxp0 paramcd rename=(y=cmax) where=(paramcd="CMAX"));
	by usubjid x;
run;

%put _user_;


```

## 2. 输出的数据集：

![../../../Z appendix/Pasted image 20230125215135.png](/img/user/Z%20appendix/Pasted%20image%2020230125215135.png)

---

# 3. 绘图

## 1. 绘图的sas代码

```sas
proc template;
	define statgraph xx;
        dynamic lixmin lixmax lixcre
				liy1min liy1max liy1cre
				liy2min liy2max liy2cre
				aucp0 aucr0 cmaxp0 cmaxr0
;
		begingraph/designwidth=10in designheight=5in;
		entrytitle "标题是："; 
		entryfootnote halign=left "脚注是：";
			layout lattice/rows=1 columns=2 columngutter=7;  
				cell;
					cellheader; /*每个部分定义cell，再cell中定义entry作为cell的标题，再对cell定义layout画其中的图*/
	                	entry "AUC线型图" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/walldisplay=all xaxisopts=(type=linear label=" 采集时间（天）" 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=lixmin  viewmax=lixmax 
	                                    tickvaluesequence=(start=lixmin end=lixmax  increment=lixcre)))
									yaxisopts=(type=linear label=" " 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=liy1min  viewmax=18.2
	                                    tickvaluesequence=(start=liy1min end=18.2  increment=liy1cre)));
										drawtext textattrs=(size=10 family="Times New Roman") "Regression Equation: Log"{sub "10"} 
											"{Y} = a + b*Log"{sub "10"} "{X}" /anchor=topleft x=20 y=100 width=100;
	                                    drawtext textattrs=(size=10 family="Times New Roman") "90% CI of slope = {low, up.}"
											/anchor=topleft x=20 y=95 width=100;
	                                    drawtext textattrs=(size=10 family="Times New Roman") "R"{sup "2"} " = R2" 
											/anchor=topleft x=20 y=90 width=100;

									modelband "myclm1"/fillattrs=(color=gray transparency = 0.7);
									scatterplot y=auclst x=x/ primary=true markerattrs=(size=4px symbol=circlefilled color=black);
									regressionplot y=auclst x=x/alpha=0.05 clm="myclm1" lineattrs=(thickness=1 color=black);
									
									layout gridded/ columns=1 valign=center autoalign=(bottomright) border=true;
							            entry halign=left "r = " aucr0/ textattrs=(size=10 family="Times New Roman");
										entry halign=left "p = " aucp0/ textattrs=(size=10 family="Times New Roman");
									endlayout;
/*									mergedlegend "a" "b"/location=inside halign=right valign=top across=1;*/
					endlayout;
				endcell;

				cell;
					cellheader;
	                	entry "CMAX线性图" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/walldisplay=all xaxisopts=(type=linear label=" " 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=lixmin  viewmax=lixmax 
	                                    tickvaluesequence=(start=lixmin end=lixmax  increment=lixcre)))
									yaxisopts=(type=linear label="xxxxx" 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=liy2min  viewmax=liy2max 
	                                    tickvaluesequence=(start=liy2min end=liy2max  increment=liy2cre)));

									drawtext textattrs=(size=10 family="Times New Roman") "CAR+" 
										/*可自定义制作纵坐标轴，尤其是带特殊字符，写在label里不生效的*/
											textattrs=(size=10 family="宋体") "细胞数("
										    textattrs=(size=10 family="Times New Roman") "CAR+"
										    textattrs=(size=10 family="宋体") "细胞数"
										    textattrs=(size=10 family="Times New Roman") "/μL)"/ x=50 y=-15 
											    anchor=bottom xspace=wallpercent yspace=wallpercent rotate=0 width=100 justify=center;

									modelband "myclm2"/fillattrs=(color=gray transparency = 0.7);
									scatterplot y=cmax x=x/ primary=true markerattrs=(size=4px symbol=circlefilled color=black);
									regressionplot y=cmax x=x/alpha=0.05 clm="myclm2" lineattrs=(thickness=1 color=black);
/*									mergedlegend "a" "b"/location=inside halign=right valign=top across=1;*/

									layout gridded/ columns=1 valign=center autoalign=(bottomright) border=true;
							            entry halign=left "r = "cmaxr0/ textattrs=(size=10 family="Times New Roman");
										entry halign=left "p = "cmaxp0/ textattrs=(size=10 family="Times New Roman");
									endlayout;

					endlayout;

				endcell;
/*				drawtext textattrs=(size=10 family="宋体") "剂量组 = " trt01a /x=50 y=0 anchor=bottom rotate=0 width=100 justify=center;*/

			endlayout;
		endgraph; 
	end;
run;

option mprint mlogic symbolgen;
ods listing close;
options orientation=landscape ps=160 ls=80;
ods escapechar="@";
ods graphics /reset noborder imagefmt=jpg noscale;
%rtfinit(pgmname=f-mean, pgmid=1, outdir=&_rootpath.\user\cuifan\PCPK,style=rtftable,bodytitle=Y);

%put _user_;
proc sgrender data=final template=xx;
	dynamic lixmin="0.6" lixmax="3" lixcre="0.5"
			liy1min="17.8" liy1max="18.1" liy1cre="0.03"
			liy2min="10.6" liy2max="11.1" liy2cre="0.1"
			aucp0="&aucp0" aucr0="&aucr0" cmaxp0="&cmaxp0" cmaxr0="&cmaxr0"
;
run;


ods _all_ close;
ods listing;

%rtfpost(pgmname=f-mean,pgmid=1,outdir=&_rootpath.\user\cuifan\PCPK);

```

## 2. 绘图的核心sas代码

>
> `1.modelband(拟合带)+scatterplot(散点&拟合线）`
> ---

```ad-note
collapse: open
title: 2.drawtext对图片注释

![Pasted image 20230125221143.png](/img/user/Z%20appendix/Pasted%20image%2020230125221143.png)
- ---

`drawtext绘制文本内容，有用unicode。以一个drawtext表示绘制一行文本，下一个drawtext绘制内容换下一行`
`drawtext` textattrs=(size=10 family="Times New Roman")  "Regression Equation: Log"{sub "10"} "{Y} = a + b\*Log"{sub "10"} "{X}"  /anchor=topleft x=20 y=100 width=100;
 `drawtext` textattrs=(size=10 family="Times New Roman") "90% CI of slope = {low, up.}"/anchor=topleft x=20 y=95 width=100;
 `drawtext` textattrs=(size=10 family="Times New Roman") "R"{sup "2"} " = R2" /anchor=topleft x=20 y=90 width=100;
```


> `3.layout gridded对图片添加图例框：`
> 
> layout gridded/ columns=1 valign=center autoalign=(bottomright) border=true; 
		entry halign=left "r = " aucr0/ textattrs=(size=10 family="Times New Roman");
		entry halign=left "p = " aucp0/ textattrs=(size=10 family="Times New Roman");
	endlayout;

 ![../../../Z appendix/Pasted image 20230125222054.png](/img/user/Z%20appendix/Pasted%20image%2020230125222054.png)




