---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["#002-SAS/02-GTL"],"permalink":"/002-SAS/02-GTL/GTL便笺/","dgPassFrontmatter":true}
---


## 1. GTL Dynamic Var

[GTL系统学习网站](https://support.sas.com/rnd/datavisualization/gtl/overview_sect19.htm)  
[官方GTL学习](https://support.sas.com/rnd/datavisualization/gtl)

## 2. unicode in GTL

>  换行没找到，可用多个entry/(drawtext?)，第二个entry/是换行效果

### 2.1. enrry
Entry textattrs=(style=italic) "E(Y)" 
	`textattrs=()/*清除前面的斜体，后面的等号，清除格式*/` " = "
       {unicode beta} {sub "0"} " + "
       {unicode beta} {sub "1"} "x" {sub "1"}
       " + " {unicode beta} {sub "2"} "x" {sub "2"}; 
       ![../../Z appendix/Pasted image 20221216223553.png](/img/user/Z%20appendix/Pasted%20image%2020221216223553.png)

### 2.2. draw text

- [参考网站](https://blogs.sas.com/content/graphicallyspeaking/2013/01/28/unicode-tick-values-using-gtl/)
![../../Z appendix/Pasted image 20230119105706.png](/img/user/Z%20appendix/Pasted%20image%2020230119105706.png)

barchart *x=cat y=Response*/ stat=mean dataskin=gloss; 

drawtext *'~{unicode "03a3"x}'* / `x='SIGMA' y=-1` anchor=top `xspace=datavalue yspace=wallpercent`;

drawtext 'r' {sup "2"} /  x='RSquare' y=-1 anchor=top xspace=datavalue yspace=wallpercent;

drawtext '~{unicode "03b1"x} + ~{unicode "03b2"x}' / x='Alpha+Beta' y=-1 anchor=top xspace=datavalue yspace=wallpercent;

drawtext '~{unicode "03c3"x}' {sub "1"} / x='Sigma1' y=-1 anchor=top xspace=datavalue yspace=wallpercent;

drawtext '~{unicode "2264"x} 10'  /  x='LE10' y=-1 anchor=top xspace=datavalue yspace=wallpercent;


## 3. 坐标轴

https://www.jianshu.com/p/a3e90268c1ca

### 3.1. 线性轴的范围设置

- 需要先写xaxisopts=(  xaxisopts=(  )  )里
- viewmin=1 viewmax=5 **tickvaluesequence=(start=1 end=5 increment=1)** `可不写viewmin/viewmax`
- viewmin=0.1 viewmax=250 **tickvaluelist=(0 100 140 220)**  `可不写viewmin/viewmax`
- **tickvaluelist=(0 100 140 220)** **tickdisplaylist=("基线" "第10周（治疗8周后）" "第14周（治疗12周后）" "第22周（治疗20周后）")**

xaxisopts=(
linearopts=(`tickvaluelist=(0 100 140 220) `
`tickdisplaylist=("基线" "第10周（治疗8周后）" "第14周（治疗12周后）" "第22周（治疗20周后）")`)

tickvalueattrs=(size=8) labelattrs=(size=10)  label="时间")

均值图和PK平均图类似，要求横坐标不等距-->用线性坐标，但需要显示文本txt

![../../Z appendix/Pasted image 20230125201310.png](/img/user/Z%20appendix/Pasted%20image%2020230125201310.png)  

## 4. 定制图例显示---LLOQ

- 通过legenditem & layout lattice & layout gridded & mergelegend 定制图例：
	- ![../../Z appendix/Pasted image 20230522170603.png](/img/user/Z%20appendix/Pasted%20image%2020230522170603.png)

````ad-note
collapse: false
title: 定制图例也显示LLOQ
```sas
data dummy;
	length grp $200;
	grp="A";
	tpt=1;aval=1;output;
	tpt=2;aval=5;output;
	tpt=3;aval=10;output;

	grp="B";
	tpt=1;aval=0.5;output;
	tpt=2;aval=3;output;
	tpt=3;aval=200;output;
run;

proc template;
	define statgraph test;
        dynamic trt01a randnum;
		begingraph/designwidth=10in designheight=5in;
			
			legenditem type=line name="a" / lineattrs=(color=blue) label="C1";
			legenditem type=line name="b" / lineattrs=(color=green) label="C3";
			legenditem type=line name="ref" / lineattrs=(color=black pattern=dash) label="LLOQ";
				
			layout lattice/rows=2 columns=1 columndatarange=union rowweights=(0.95 0.05 );
			layout lattice/rows=1 columns=2;  
				cell;
					cellheader; 
						/*每个部分定义cell，再cell中定义entry作为cell的标题，再对cell定义layout画其中的图*/
	                	entry "线性标度" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/walldisplay=all xaxisopts=(type=linear label="采集时间（天）" 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 
		                                   /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=1 viewmax=5
	                                    tickvaluesequence=(start=1 end=5 increment=1)))
									yaxisopts=(type=linear label=" " 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 
		                                   /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=0.1 viewmax=250
	                                    tickvaluesequence=(start=0.1 end=250 increment=50)));
										drawtext textattrs=(size=10 family="Times New Roman") "CAR+"  
											/*可自定义制作横坐标轴，尤其是带特殊字符，写在label里不生效的*/
											textattrs=(size=10 family="宋体") "细胞数("
										    textattrs=(size=10 family="Times New Roman") "CAR+"
										    textattrs=(size=10 family="宋体") "细胞数"
										    textattrs=(size=10 family="Times New Roman") "/μL)"
										    / x=-10.5 y=50 anchor=bottom xspace=wallpercent yspace=wallpercent rotate=90 width=100 justify=center;

									seriesplot y=aval x=tpt/group=grp name="a";
									scatterplot y=aval x=tpt/group=grp name="b";
									referenceline y=0.8/name="ref" lineattrs=(color=black pattern=dash);
/*									mergedlegend "a" "b"/location=inside halign=right valign=top across=1;*/
					endlayout;
				endcell;

				cell;
					cellheader;
	                	entry "半对数标度" / textattrs=(size=10pt weight=bold);
					endcellheader;
					layout overlay/xaxisopts=(type=linear label=" " 
	                                   tickvalueattrs=(size=8) labelattrs=(size=10 
			                                   /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   linearopts=(viewmin=1 viewmax=5
	                                    tickvaluesequence=(start=1 end=5 increment=1)))
									yaxisopts=(type=log label="人血清白蛋白 (μg/mL)" 

	                                   tickvalueattrs=(size=8) labelattrs=(size=10 
			                                   /*family="Times New Roman"中文的设置这个就会中文出现口口*/)
	                                   logopts=(base=10 viewmin=0.1 viewmax=250
	                                    tickvaluelist=(0.1 1 10 50 100 250)));

									drawtext textattrs=(size=10 family="Times New Roman") "CAR+" 
												/*可自定义制作纵坐标轴，尤其是带特殊字符，写在label里不生效的*/
											textattrs=(size=10 family="宋体") "细胞数("
										    textattrs=(size=10 family="Times New Roman") "CAR+"
										    textattrs=(size=10 family="宋体") "细胞数"
										    textattrs=(size=10 family="Times New Roman") "/μL)"/ x=50 y=-10.5 anchor=bottom xspace=wallpercent yspace=wallpercent rotate=0 width=100 justify=center;

									seriesplot y=aval x=tpt/group=grp name="a";
									scatterplot y=aval x=tpt/group=grp name="b";
									referenceline y=0.8/name="ref" lineattrs=(color=black pattern=dash);
					endlayout;
				endcell;

			endlayout;
					 
					layout gridded / columns=2 border=false ; /*定制右边图例*/
/*							entry textattrs=(size=8) halign=left "总体疗效评价：";*/
                            mergedlegend  "a" "b" / across=2 valueattrs=(size=8) 
		                             location=inside border=false halign=left valign=bottom;
							discretelegend "ref"/  border=false  ;
                     endlayout;			
			endlayout;
		endgraph; 
	end;
run;

ods rtf file="D:\Users\C02207\Desktop\项目\t.rtf";
proc sgrender data=dummy template=test;
run;
ods rtf close;
ods listing;
ods html;
```
````

## 5. lattice按照比例分配占比（一列）

layout lattice/***rows=2 columns=1 columndatarange=union rowweights=(0.95 0.05 );***

## 6. 提高分辨率

> - 写在ods rtf这类输出对象上的选项，image_dpi=300。rtf默认100. 
> 	- ods rtf file="xxxx" style=rtftable `image_dpi=300`;
> 	- 书中参考：[[obsidian://booknote?type=annotation&book=100 PDF/SAS9.4 GTL User-Guide 简版.pdf&id=98c4cdf9-f8e0-6038-022e-4181010bb2c7&page=586&rect=239.277,164.926,502.849,201.570\|IMAGE_DPI=numberSpecifies the image resolution indots per inch for output images.IMAGE_DPI=200 is the default.]]

> - rtf转成pdf，可能会引起dpi下降，可这样处理
> 	- ![../../Z appendix/Pasted image 20230522175011.png](/img/user/Z%20appendix/Pasted%20image%2020230522175011.png)