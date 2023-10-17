---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/02 GTL/项目中的/流程图-flowchart/","dgPassFrontmatter":true}
---


# 1.输出结果

![Pasted image 20230101115406](/img/user/Z appendix/Pasted image 20230101115406.png)

每个框的文本用drawtext画，图框的位置，线条/箭头的位置自定义。

---

# 2.整理数据

```sas
data adsl;
      length label $200.;
     set adam.adsl;
      where scrfafl ne " ";
    *筛选;
     ord=1;
      label="筛选";
      labeln=1;
     output;
     *筛选失败及原因;
      if scrfafl="Y" then do;
          ord=2;
        label="筛选失败";
         labeln=1;
          output;
      end;
/*    if scrfafl="Y" then do;*/
/*          ord=2;*/
/*          label="*"||strip(scrreas);*/
/*          labeln=scrreasn;*/
/*          output;*/
/*     end;*/
     *入组;
/*     if enrlfl="Y" then do;*/
/*          ord=3;*/
/*         label="入组";*/
/*         labeln=1;*/
/*          output;*/
/*      end;*/
      *剂量组;
   if enrlfl="Y" and trt01p="20 mg" then do;
	  ord=3;
	  label="20 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="40 mg" then do;
	  ord=4;
	  label="40 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="60 mg" then do;
	  ord=5;
	  label="60 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="80 mg" then do;
	  ord=6;
	  label="80 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="100 mg" then do;
	  ord=7;
	  label="100 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="120 mg" then do;
	  ord=8;
	  label="120 mg入组";
	  labeln=1;
	  output;
  end;

  if enrlfl="Y" and trt01p="140 mg" then do;
	  ord=9;
	  label="140 mg入组";
	  labeln=1;
	  output;
  end;

     *至少接受一次试验用药品;

if trtonefl="Y" and trt01p="20 mg" then do;
 ord=10;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="40 mg" then do;
ord=11;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="60 mg" then do;
ord=12;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="80 mg" then do;
ord=13;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="100 mg" then do;
 ord=14;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="120 mg" then do;
ord=15;
label="至少接受一次试验药物";
labeln=1;
output;
end;

if trtonefl="Y" and trt01p="140 mg" then do;
ord=16;
label="至少接受一次试验药物";
labeln=1;
output;
end;

 *提前退出及原因;
if trt01p="20 mg" then do;
      if eosstt="中止" then do;
          ord=17;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=17+0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="40 mg" then do;
      if eosstt="中止" then do;
          ord=18;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=18++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="60 mg" then do;
      if eosstt="中止" then do;
          ord=19;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=19++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="80 mg" then do;
      if eosstt="中止" then do;
          ord=20;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=20++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="100 mg" then do;
      if eosstt="中止" then do;
          ord=21;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=21++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="120 mg" then do;
      if eosstt="中止" then do;
          ord=22;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=22++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

if trt01p="140 mg" then do;
      if eosstt="中止" then do;
          ord=23;
        label="提前退出";
         labeln=0;
          output;
      end;
    if eosstt="中止" and not missing(dcsreas) then do;
          ord=23++0.01*dcsreasn;
          label="*"||strip(dcsreas);
          labeln=dcsreasn;
          output;
     end;
end;

   *完成试验;

 if eosstt="完成" and trt01p="20 mg" then do;
 ord=24;
 label="完成试验";
 labeln=1;
 output;
 end;

 if eosstt="完成" and trt01p="40 mg" then do;
 ord=25;
 label="完成试验";
 labeln=1;
 output;
 end;

 if eosstt="完成" and trt01p="60 mg" then do;
 ord=26;
 label="完成试验";
 labeln=1;
 output;
 end;

 if eosstt="完成" and trt01p="80 mg" then do;
 ord=27;
 label="完成试验";
 labeln=1;
 output;
 end;

 if eosstt="完成" and trt01p="100 mg" then do;
 ord=28;
 label="完成试验";
 labeln=1;
 output;
 end;

 if eosstt="完成" and trt01p="120 mg" then do;
 ord=29;
 label="完成试验";
 labeln=1;
 output;
 end;

  if eosstt="完成" and trt01p="140 mg" then do;
 ord=30;
 label="完成试验";
 labeln=1;
 output;
 end;
run;


  proc freq data=adsl noprint;
     table ord*labeln*label/out=freq;
  run;


  data all1;
      length name $200.;
     set freq;
     name=strip(label)||" (N = "||strip(put(count,best.))||")";
 run;

 data dummy;
 	length  _1col _2col _3col _4col $400 col $1000 col1 $200;
	_1col="筛选失败 (N = 0);20 mg入组 (N = 0);40 mg入组 (N = 0);60 mg入组 (N = 0);80 mg入组 (N = 0);100 mg入组 (N = 0);120 mg入组 (N = 0);140 mg入组 (N = 0);";
	_2col="至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);至少接受一次试验药物 (N = 0);";
	_3col="提前退出 (N = 0);提前退出 (N = 0);提前退出 (N = 0);提前退出 (N = 0);提前退出 (N = 0);提前退出 (N = 0);提前退出 (N = 0);";
	_4col="研究结束 (N = 0);研究结束 (N = 0);研究结束 (N = 0);研究结束 (N = 0);研究结束 (N = 0);研究结束 (N = 0);研究结束 (N = 0)";
	col=cats(_1col,_2col,_3col,_4col);
	do ord=2 to 30;
		col1=kscan(col,ord-1,";");
		output;
	end;
	keep ord col1;
run;

data all2;
	merge dummy all1;
	by ord;
	ord=int(ord);
	name=coalescec(name,col1);
run;

 proc transpose data=all2 out=all2_trans;
      by ord;
     var name;
  run;

  data final;
      set all3 arrow;
      keep name: x y m: n:;
  run;

```

---

# 3.指定坐标

```sas
*-----------------------------------------------------------------;
 *text文本的坐标;
data all3;
     length name $600.;
      set all2_trans;
      name=catx(" #       ",of col:);
      if ord=1 then do;   x=40;   y=70;   end; /*筛选*/
      if ord=2 then do;   x=54;   y=63;   end; /*筛选失败*/
      *入组;
	  if ord=3 then do;x=0;y=50;end;
	  if ord=4 then do;x=16;y=50;end;
	  if ord=5 then do;x=32;y=50;end;
	  if ord=6 then do;x=48;y=50;end;
	  if ord=7 then do;x=66;y=50;end;
	  if ord=8 then do;x=83;y=50;end;
	  if ord=9 then do;x=100;y=50;end;

	  *至少一次试验药物给药;
	  if ord=10 then do;  x=0;    y=38;   end;
      if ord=11 then do;  x=16;   y=38;   end;
      if ord=12 then do;  x=32;   y=38;   end;
      if ord=13 then do;  x=48;   y=38;   end;
      if ord=14 then do;  x=66;   y=38;   end;
      if ord=15 then do;  x=83;   y=38;   end;
      if ord=16 then do;  x=99;  y=38;   end;

	  *提前退出及原因;
	
	  if ord=17 then do;  x=0;    y=25;   end;
      if ord=18 then do;  x=16;   y=25;   end;
      if ord=19 then do;  x=32;   y=25;   end;
      if ord=20 then do;  x=48;   y=25;   end;
      if ord=21 then do;  x=66;   y=25;   end;
      if ord=22 then do;  x=83;   y=25;   end;
      if ord=23 then do;  x=99;  y=25;   end;

	  *完成试验;
	  if ord=24 then do;  x=0;    y=10;   end;
      if ord=25 then do;  x=16;   y=10;   end;
      if ord=26 then do;  x=32;   y=10;   end;
      if ord=27 then do;  x=48;   y=10;   end;
      if ord=28 then do;  x=66;   y=10;   end;
      if ord=29 then do;  x=83;   y=10;   end;
      if ord=30 then do;  x=99;  y=10;   end;
      
      /*有的太长放不下，可换行*/
	  if find(name,"至少接受一次") then name=tranwrd(name,"至少接受一次试验药物 (N","至少接受一次#试验药物 (N"); 
	  if find(name,"受试者出现疾病进展 (N") then name=tranwrd(name,"受试者出现疾病进展 (N","受试者出现#疾病进展 (N"); 
	  
      keep ord name x y;
  run;

*-----------------------------------------------------------------;
 *arrow的坐标;

  data arrow;
/*    第一行向右箭头*/
    m1=40;  n1=63;   m2=47;   n2=63;  output;
/*    第二行箭头*/
    m1=0;   n1=57;   m2=0;    n2=53;   output;
    m1=16;  n1=57;   m2=16;   n2=53;   output;
    m1=32;  n1=57;   m2=32;   n2=53;   output;
    m1=48;  n1=57;   m2=48;   n2=53;   output;
    m1=66;  n1=57;   m2=66;   n2=53;   output;
    m1=83;  n1=57;   m2=83;   n2=53;   output;
    m1=99;  n1=57;   m2=99;   n2=53;   output;
/*    第三行箭头*/
    m1=0;   n1=47;    m2=0;    n2=42;    output;
    m1=16;  n1=47;    m2=16;   n2=42;    output;
    m1=32;  n1=47;    m2=32;   n2=42;    output;
    m1=48;  n1=47;    m2=48;   n2=42;    output;
    m1=66;  n1=47;    m2=66;   n2=42;    output;
    m1=83;  n1=47;    m2=83;   n2=42;    output;
    m1=99;  n1=47;    m2=99;   n2=42;    output;
/*    第四行箭头*/
/*    m1=0;   n1=34;    m2=0;    n2=29;    output;*/
/*    m1=16;  n1=34;    m2=16;   n2=29;    output;*/
/*    m1=32;  n1=34;    m2=32;   n2=29;    output;*/
/*    m1=48;  n1=34;    m2=48;   n2=29;    output;*/
/*    m1=66;  n1=34;    m2=66;   n2=29;    output;*/
/*    m1=83;  n1=34;    m2=83;   n2=29;    output;*/
/*    m1=99;  n1=34;    m2=99;   n2=29;    output;*/
/*    第五行箭头*/
/*    m1=0;   n1=20.5;    m2=0;    n2=13;    output;*/
/*    m1=16;  n1=20.5;    m2=16;   n2=13;    output;*/
/*    m1=32;  n1=20.5;    m2=32;   n2=13;    output;*/
/*    m1=48;  n1=20.5;    m2=48;   n2=13;    output;*/
/*    m1=66;  n1=20.5;    m2=66;   n2=13;    output;*/
/*    m1=83;  n1=20.5;    m2=83;   n2=13;    output;*/
/*    m1=99;  n1=20.5;    m2=99;   n2=13;    output;*/
/*四五行写一个箭头*/
    m1=0;   n1=34;    m2=0;    n2=13;    output;
    m1=16;  n1=34;    m2=16;   n2=13;    output;
    m1=32;  n1=34;    m2=32;   n2=13;    output;
    m1=48;  n1=34;    m2=48;   n2=13;    output;
    m1=66;  n1=34;    m2=66;   n2=13;    output;
    m1=83;  n1=34;    m2=83;   n2=13;    output;
    m1=99;  n1=34;    m2=99;   n2=13;    output;
    keep m: n:;
  run;

```

 ![Pasted image 20230101115619](/img/user/Z appendix/Pasted image 20230101115619.png)

---

# 4.GTL画流程图

```sas
 ** template **;
  proc template;
      define statgraph flowchart;
          begingraph/ designwidth=1200 designheight=660;
              layout overlay / yaxisopts=(linearopts=(viewmin=10 viewmax=70) display=none)
              xaxisopts=(linearopts=(viewmin=0 viewmax=100) display=none)
              walldisplay=none;

                vectorplot xorigin=m1 yorigin=n1 x=m2 y=n2 / xaxis=x yaxis=y
                      arrowdirection=out
                      arrowheadshape=open
                      lineattrs=(pattern=solid thickness=1px color=black);

               textplot x=x y=y text=name / name='m' /*目前没啥用*/
                          splitpolicy=split/*根据给定的split拆分长文本*/
                          splitchar='#'/*指定split字符*/
                          splitchardrop=true /*文本不显示split字符*/
                          splitjustify=left
                          splitpolicy=splitalways /*感觉可以不用，和前重复*/
                          vcenter=bbox /*文本是相对于文本包围框垂直居中*/
                          position=center /*和vcenter一起用*/
                          display=(fill outline)
                          outlineattrs=(color=black)
                          pad=(top=0.1in bottom=0.1in left=0.1in right=0.1in) /*指定文本框内部四周到框线空余的空间*/
                          fillattrs=(color=white transparency=0)
                          textattrs=(size=7 color=black);

			   drawline x1=0 y1=57 x2=99 y2=57 / xaxis=x yaxis=y drawspace=datavalue
                    lineattrs=(pattern=solid thickness=1px color=black);  /*x1,y1为起点坐标。x2,y2为终点坐标，画两点坐标的线段*/

			   drawline x1=40 y1=68 x2=40 y2=57 / xaxis=x yaxis=y drawspace=datavalue
                    lineattrs=(pattern=solid thickness=1px color=black);

              endlayout;
          endgraph;
      end;
  run;

  ods listing close;
  ods graphics / reset noborder imagefmt=jpg noscale;
  ods html close;
  proc sgrender data=final template=flowchart;
  run;

```

---

# 5. 绘图核心代码

> - textplot和vectorplot，<mark style="background: #FFF3A3A6;">根据数据中的坐标点产生文字及箭头</mark>，基本选项不需要更改
> 	- <mark style="background: #FFF3A3A6;">textplot</mark>：
> 		- [Use the TEXTPLOT statement, rather than the SCATTERPLOT statement with the MARKERCHARACTER= option, when you want more control over the appearance ofthe text](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=2493ec64-db20-af36-b436-15bcf79b1910&page=971&rect=148.997,249.676,507.560,286.320)
> 		- [Example: TEXTPLOT Statement](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=49a55234-4705-af4a-f6e4-32463d1a74f3&page=999&rect=149.008,397.045,430.783,422.999)
> 		- textplot x=x y=y text=name / name='m'   /*目前没啥用*/ 
> 		        splitpolicy=split                                            /*根据给定的split拆分长文本*/
> 		        splitchar='#'                                                   /*指定split字符*/
> 		        splitchardrop=true                                   /*文本不显示split字符*/
> 		        splitjustify=left
> 		        splitpolicy=splitalways
 >
> 	- <mark style="background: #FFF3A3A6;">vectorplot</mark>:
> 		- [Creates a plot of vectors (directed line segments).](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=3b105e13-4d73-1059-0bab-30d0b75eb4da&page=1000&rect=71.999,454.676,281.607,467.320) **-->有方向**
> 		- [Example Program](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=6a63f581-99c0-6451-3b1b-0077ff71ba11&page=1017&rect=72,401.545,228.039,427.499)
> 		- vcenter=bbox                                                   /*文本是相对于文本包围框垂直居中*/ 
> 	         position=center                                            /*和vcenter一起用*/ 
> 	         display=(fill outline) 
> 	         outlineattrs=(color=black)
> 	         pad=(top=0.1in bottom=0.1in left=0.1in right=0.1in) /*指定文本框内部四周到框线空余的空间*/
> 	         fillattrs=(color=white transparency=0)
> 	         textattrs=(size=7 color=black);
 > 
> - drawline<mark style="background: #FFB86CA6;">画线段</mark>
> 	- <mark style="background: #FFB86CA6;">drawline</mark>，
> 		- [DRAWLINE Statement](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=75568ba0-bd7c-cfe5-0282-ee2c078cce7a&page=1416&rect=72.006,124.545,268.508,150.499)
> 		- [Example Program](obsidian://booknote?type=annotation&book=100%20PDF/SAS9.4%20GTL.pdf&id=e0311100-c9a4-a6cb-17dc-d50f5e20924e&page=1422&rect=72,85.545,228.039,111.499)
> 		- drawline x1=40 y1=68 x2=40 y2=57 / xaxis=x yaxis=y drawspace=datavalue lineattrs=(pattern=solid thickness=1px color=black);







