---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/KM图/","dgPassFrontmatter":true}
---


# 1. 输出结果

![../../../Z appendix/Pasted image 20221211111916.png](/img/user/Z%20appendix/Pasted%20image%2020221211111916.png)

---

# 2. 生存分析产生数据

## 1. 生存分析产生数据-sas代码：

```sas
%macro kmplot(cd=,label=,id=,out=,eval=);

proc datasets lib=work mt=data kill nolist nowarn;
quit;

data adttee;
    set adam.adttee;
    where ittfl="Y" and paramcd="&cd." and parqual="&eval.";
run;

/*Max month (integer in every 1 months)*/
proc sql noprint;
    select ceil(max(aval)/1)*1 into :maxmth from adttee;
/*    select ceil(max(aval)) into :maxmth from adttee;*/
quit;

%put &maxmth.;

/*Survival Analysis*/
ods graphics on;
ods listing close;
ods output survivalplot=survival quartiles=quartiles;
proc lifetest data=adttee plots=(survival(cl atrisk=0 to &maxmth. by 1));
    time aval*cnsr(1);
    strata trt01pn;
run;
ods output close;
ods listing;

data final (keep=time tatrisk trt01pn trt01p trt01p2 survival_plot censor_plot atriskc);
    set survival;
	by stratumnum;
    retain survival_plot revent;/*revent为当前所有的event总和*/
    length atriskc trt01p2 $200;
    if not missing(survival) then survival_plot=survival;
    if not missing(censored) then censor_plot=survival_plot;
    if first.stratumnum=1 then revent=0;
    if not missing(event) then revent=revent+event;
    trt01pn=stratumnum;
    if trt01pn=1 then do;trt01p="AK112 20mg/kg Q3W + Pemetrexed + Carboplatin";trt01p2="AK112";end;
		else if trt01pn=2 then do;trt01p="Placebo + Pemetrexed + Carboplatin";trt01p2="Placebo";end;
    atriskc=cats(atrisk)||" ("||cats(revent)||')';
    drop revent;
run;

```

## 2. 输出的数据集：

[[03 STAT/项目中的/生存分析/01 生存分析-Kaplan-Meier估计生存时间及not-event率#2.2 输出no-event的数据集，及扩展详解\|01 生存分析-Kaplan-Meier估计生存时间及not-event率#2.2 输出no-event的数据集，及扩展详解]]

---

# 3. 绘图

## 1. 绘图的sas代码

```sas

/*Plots*/;
proc template;
    define statgraph km_plot;
        begingraph /backgroundcolor=white designwidth=1200px designheight=650px;
            layout lattice/columndatarange=union rowweights=(0.04 0.8 0.06 0.1 );/*以下分成4部分*/

	            layout gridded/ columns=1 valign=center autoalign=(topright) halign=right border=false location=outside;
	              	entry halign=right "+：删失"/ textattrs=(size=10);  /*第一部分是+删失的图例*/

	            endlayout;

                layout overlay/                                       /*第二部分是图的主体*/
                    yaxisopts =(label=&label.
                        labelattrs=(size=10) offsetmin=0 offsetmax=0
                        linearopts=(viewmin=0 viewmax=1.05
                        tickvaluelist=(0 0.2 0.4 0.6 0.8 1)
                        tickdisplaylist=("0" "20" "40" "60" "80" "100"))
                        tickvalueattrs=(size=10)
                        )
                    xaxisopts =(label="时间（月）"
                        labelattrs=(size=10) offsetmin=0 offsetmax=0
                        linearopts=(viewmin=0 viewmax=&maxmth tickvaluelist=(%do i=0 %to &maxmth. %by 1; &i %end;))
                        tickvalueattrs=(size=10)
                        );
                    stepplot x=time y=survival_plot/ name="step1" group=trt01p;
                    scatterplot x=time y=censor_plot/ name="step2" group=trt01p markerattrs=(size=10 symbol=plus);
	                mergedlegend "step1" "step2"/ location=outside halign=right valign=top border=false across=1 valueattrs=(size=8);

/*	                layout gridded/ columns=1 valign=center autoalign=(bottomleft) border=false;*/
/*		                entry halign=left "治疗组："/ textattrs=(size=10);*/
/*		                discretelegend "step1"/ location=inside halign=left valign=top border=false across=2 valueattrs=(size=8);*/
/*	                endlayout;*/

                endlayout;

                layout overlay;                                    /*第三部分是'风险中的xx'，*/                        
                	entry halign=left "处于风险中的受试者数（事件数）"/ textattrs=(size=10);
                endlayout;

                layout overlay/ xaxisopts=(display=none linearopts=(tickvaluelist=(%do i=0 %to &maxmth. %by 1; &i %end;)))                                  /*第四部分是风险中的xx的具体数据*/
                	yaxisopts=(tickvalueattrs=(size=7) tickvaluehalign=left reverse=true ) walldisplay=none;
/*                    scatterplot x=tatrisk y=trt01p/ group=trt01p markercharacter=atriskc markercharacterattrs=(size=7);*/
					axistable value=atriskc x=tatrisk /class=trt01p2 colorgroup=trt01p2 ;
       			endlayout;

            endlayout;
        endgraph;
    end;
run;

/*Output*/
ods listing close;
ods graphics/ reset noborder imagefmt=jpg noscale;
%rtfinit(pgmname=f-km, pgmid=&id., outdir=&_rootpath.\user\cuifan);

proc sgrender data=final template=km_plot;
quit;
ods _all_ close;
ods listing;
 
%rtfpost(pgmname=f-km, pgmid=&id., outdir=&_rootpath.\user\cuifan);

%mend;

option mprint;
%kmplot(cd=PFS,label=%str('无进展生存率 (%)'),id=1,out=01_01_01,eval=INDEPENDENT ASSESSOR);

```

## 2. 绘图的核心sas代码

> 绘图的核心sas代码
> - `layout grid，画文本`（`1` 有用到, 3主要是用layout的entry做的，用grid应该可以，就是位置可能不是那么居左，位置不好看）
> 
> - stepplot画线图，加上`scatterplot将删失点画进来`（`2`部分）-->注意其中`以time作为x，其中time是连续的`
> 
> - `axistable`，做`沿轴的数据列表`（`4`部分）。颜色可变等可加option。`最左AK112/Placebo是reverse了y轴的tickvalue(显示为trt01p2值)，在yaxisopts设置`。-->注意这个是`以tatrisk作为横坐标`，是离散的，不应该用time
> 	  - <mark style="background: #FFF3A3A6;">axistable value=atriskc x=tatrisk /class=trt01p2 colorgroup=trt01p2 ;</mark>
> 	      [[obsidian://booknote?type=annotation&book=100 PDF/SAS9.4 GTL User-Guide 简版.pdf&id=d63fe3fa-0a37-03cb-36ff-9ddcf3eeb26c&page=105&rect=149.000,242.869,512.150,268.179\|参考AXISTABLEstatement]]
> 	  - 也可用scatterplot，最左边的text为yaxis-tickvalue，显示这个就有了，只是可能没有颜色变化了，只有黑色的value值

