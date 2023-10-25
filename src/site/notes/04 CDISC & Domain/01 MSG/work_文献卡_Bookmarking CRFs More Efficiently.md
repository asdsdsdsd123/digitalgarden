---
{"date":"2023/9/24","parent":"Bookmarking CRFs More Efficiently","collections":["4）MSG (acrf & define)"],"version":0,"libraryID":1,"itemKey":"TFA5V7NL","dg-publish":true,"permalink":"/04 CDISC & Domain/01 MSG/work_文献卡_Bookmarking CRFs More Efficiently/","dgPassFrontmatter":true}
---

work\_文献卡\_Bookmarking CRFs More Efficiently

## 1. 💡 Meta Data

| Items       | Value                                                                       |
| ----------- | --------------------------------------------------------------------------- |
| Title       | Bookmarking CRFs More Efficiently                                           |
| Journal     |                                                                             |
| Tags        | #                                                                           |
| Pub. date   |                                                                             |
| IF          | Zotero                                                                      |
| Attachments | [Kim - Bookmarking CRFs More Efficiently.pdf](zotero://open-pdf/0_5SK5QLU7) |

## 2. 📜 研究概述

**摘要**：Preparing an annotated CRF (aCRF) entails adding PDF bookmarks. If done manually, this can be very time-consuming. This paper will show how to automate aspects of this process, including the specification of bookmark properties required by the FDA or CDISC (namely, nested levels and inherit zoom magnification). This paper assumes the reader knows how to edit the Windows PATH environment variable and use a Windows command line.
**标题及摘要翻译**：titleTranslation: 更高效地为 CRF 添加书签 abstractTranslation: 准备带注释的 CRF (aCRF) 需要添加 PDF 书签。如果手动完成，这可能非常耗时。本文将展示如何自动化此过程的各个方面，包括 FDA 或 CDISC 所需的书签属性规范（即嵌套级别和继承缩放放大倍数）。本文假设读者知道如何编辑 Windows PATH 环境变量并使用 Windows 命令行。

### 2.1. <span style="color: #02ab4b">自行总结：</span>

半自动方法，需要手动添加第几页是什么访视，做了什么。

## 3. 📖 方法

### 3.1. bookmarker的制作

*   先根据eCRF`手动准备`每一级的标签。内容：每个访视做什么检查，对应页码。**最终输出xlsx名为bookmarker**
    *   ![\<img alt="" data-attachment-key="PKJ8W2YQ" width="1169" height="339" src="attachments/PKJ8W2YQ.png" ztype="zimage">](/img/user/Z appendix/PKJ8W2YQ.png)

> 如果有ALS，可通过SAS自动获取访视，**生成bookmarcker**
> ```sas
> /*根据ALS，列出哪个访视做哪个表单*/
> 
> proc import datafile="V:\Telix\TLX250-CDx-CP-001\BS\csr\user\cuifan\dm_docs\ALS.xlsx" out=_als dbms=xlsx replace;
> getnames=No;
> run;
> data als;
> 	length formname $200;
> 	set _als(firstobs=2);
> 	formname=a;
> 	run;
> 	
> data _null_;
> 	length vsname $32767;
> 	set _als(obs=1);
> 	vsname=catx("|||",of c--l);
> 	call symputx('vsname',vsname);
> run;
> 
> proc sort data=als;by formname;run;
> 
> proc transpose data=als out=als2;
> 	by formname;
> 	var c--l;
> run;
> 
> data als2;
> 	length visit $200;
> 	set als2;
> 	visitseq=rank(_name_)-66
> 	visit=kscan("&vsname",visitseq,"|||");
> 	if missing(col1) then delete;
> run;
> 
> /*通过freepic2pdf导出UNIQUE BLANK CRF的书签及书签的页码*/
> 
> /*给ALS2加上页码信息*/
> proc import datafile="V:\Telix\TLX250-CDx-CP-001\BS\csr\user\cuifan\dm_docs\PAGE.xlsx" out=_page dbms=xlsx replace;
> getnames=yes;
> run;
> 
> proc sql;
> 	create table final as
> 	select a.*,pagenum
> 	from als2 as a left join _page as b on a.formname=b.formname 
> 	order by visitseq,pagenum;
> quit;
> 
> /*output*/
> data final;
> 	retain VISITSEQ VISIT FORMNAME PAGENUM;
> 	set final;
> 	keep VISITSEQ--PAGENUM;
> run;
> 
> proc export data=final outfile="V:\Telix\TLX250-CDx-CP-001\BS\csr\user\cuifan\dm_docs\bookmarker.xlsx" dbms=xlsx replace;
> run;
> ```
> - ***准备工作：***
> 	- ==ALS.xlsx==  ALS先准备成这样：`黄色内容需要补充，第一列确定为FORMNAME，结构不要改动`
> 		- ![../../Z appendix/Pasted image 20231016095946.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020231016095946.png)
> 	-  ==PAGE.xlsx== 通过FreePic2Pdf_cn获取DM_Unique_Blank_CRF的表单名字及对应页码。`注意每列名字固定，分别为FORMNAME/PAGENUM`
> 		- ![../../Z appendix/Pasted image 20231016101045.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020231016101045.png)
> - ***输出***
> 	- ==Bookmarker.xlsx==
> 		- ![../../Z appendix/Pasted image 20231016101247.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020231016101247.png)
### 3.2. SAS生成配置文件

*   通过sas，根据上面步骤的excel生成标签文件`ps。形式形如`
	* ![Pasted image 20230924222044](/img/user/Z appendix/Pasted image 20230924222044.png)
	- ![Pasted image 20230924222019](/img/user/Z appendix/Pasted image 20230924222019.png)
	- 参考：<span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2F5SK5QLU7%22%2C%22annotationKey%22%3A%22FTK4HRAQ%22%2C%22color%22%3A%22%23e56eee%22%2C%22pageLabel%22%3A%222%22%2C%22position%22%3A%7B%22pageIndex%22%3A1%2C%22rects%22%3A%5B%5B415.432%2C285.334%2C495.882%2C294.576%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FY8WQR24I%22%5D%2C%22locator%22%3A%222%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/5SK5QLU7?page=2&#x26;annotation=FTK4HRAQ">“PDFMARK syntax”</a></span><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FY8WQR24I%22%5D%2C%22locator%22%3A%222%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/Y8WQR24I">Kim, p. 2</a></span>)</span>🔤PDF标记语法🔤 


````ad-note
collapse: open
title: 
````sas

/*---------------------------------------------------------------------------------------------*/
/*------------------------------------BOOKMARKING BY VISIT-------------------------------------*/
/*---------------------------------------------------------------------------------------------*/
option mprint;
%macro xlsx_to_bookmarks_by_visit(input_xlsx=, output_ps=bm_by_visit, formsortvar=formname);
	* author : Noory Kim;
	* version : 0.1 (04APR2022);
	* parameters;
		* input_xlsx : name of input file (Excel XLSX) with bookmark data;
		* output_ps : name of output file (PostScript) with PDFMARK bookmarks;
			* default value: bm_by_visit;
		* formsortvar : sort topics alphabetically (=formname) or in order of appearance (=pagenum)
			* default value: formname;

* import source data;
proc import file="&input_xlsx..xlsx" out=_010 dbms=xlsx replace;
run;
* get counts for nesting bookmarks ;
proc sql;
create table _020 as
select *
, count(*) as count
, put(count(*), 8.) as countc
from _010
group by visitseq
order by visitseq, &FORMSORTVAR.
;
quit;
* get number of visits;
proc sql;
create table _030 as
select *
, count(unique visitseq) as n_visit
from _020
;
quit;
* sort source data;
proc sort data=_030 out=_077;
by visitseq &FORMSORTVAR.;
run;
* generate and output postscript file with PDFMARK syntax;
data _null_;
file "&output_ps..ps" ;
set _077;
by visitseq;
if _n_ = 1 then do;
put '[ /PageMode /UseOutlines /Page 1 /View [/Fit] /DOCVIEW pdfmark';
put '[ /Page 1 /View [/XYZ null null 0] /Title (Visit) /Count ' n_visit ' /OUT pdfmark';
end;
if first.visitseq = 1 then do;
put '[ /Page ' pagenum ' /View [/XYZ null null 0] /Title (' visit ') /Count ' countc ' /OUT pdfmark';
end;
put '[ /Page ' pagenum ' /View [/XYZ null null 0] /Title (' formname ') /OUT pdfmark';
run;
%mend;
* sample macro calls;
/*%xlsx_to_bookmarks_by_visit(input_xlsx=D:\Users\fan\Desktop\program studyid\bookmarker\bookmarker, 
output_ps=D:\Users\fan\Desktop\program studyid\bookmarker\bm_by_visit_formname,formsortvar=formname);*/

%xlsx_to_bookmarks_by_visit(input_xlsx=D:\Users\fan\Desktop\program studyid\bookmarker\bookmarker, 
output_ps=D:\Users\fan\Desktop\program studyid\bookmarker\bm_by_visit_pagenum,formsortvar=pagenum);

/*---------------------------------------------------------------------------------------------*/
/*--------------------------BOOKMARKING BY TOPIC (CRF FORM)------------------------------------*/
/*---------------------------------------------------------------------------------------------*/
%macro xlsx_to_bookmarks_by_topic(input_xlsx=, output_ps=bm_by_topic, formsortvar=formname);
* author : Noory Kim;
* version : 0.1 (04APR2022);
* parameters;
* input_xlsx : name of input file (Excel XLSX) with bookmark data;
* output_ps : name of output file (PostScript) with PDFMARK bookmarks;
* default value: bm_by_topic;
* formsortvar : sort topics alphabetically (=formname) or in order of appearance (=pagenum)
* default value: formname;
* import source data;
proc import file="&input_xlsx..xlsx" out=_010 dbms=xlsx replace;
run;
* get counts for nesting bookmarks ;
proc sql;
create table _020 as
select *
, count(*) as count
, put(count(*), 8.) as countc
from _010
group by formname
order by formname, visitseq
;
quit;
* get number of visits;
proc sql;
create table _030 as
select *
, count(unique formname) as n_formname
from _020
;
quit;
* sort source data;
proc sort data=_030 out=_077;
by &FORMSORTVAR. visitseq;
run;
* generate and output postscript file with PDFMARK syntax;
data _null_;
file "&output_ps..ps" ;
set _077;
by &FORMSORTVAR.;
if _n_ = 1 then do;
put '[ /PageMode /UseOutlines /Page 1 /View [/Fit] /DOCVIEW pdfmark';
put '[ /Page 1 /View [/XYZ null null 0] /Title (Forms) /Count ' n_formname ' /OUT pdfmark';
end;
if first.&FORMSORTVAR. = 1 then do;
put '[ /Page ' pagenum ' /View [/XYZ null null 0] /Title (' formname ') /Count ' countc '
/OUT pdfmark';
end;
put '[ /Page ' pagenum ' /View [/XYZ null null 0] /Title (' visit ') /OUT pdfmark';
run;
%mend;
* sample macro calls;
/*%xlsx_to_bookmarks_by_topic(input_xlsx=D:\Users\fan\Desktop\program studyid\bookmarker\bookmarker, 
output_ps=D:\Users\fan\Desktop\program studyid\bookmarker\bm_by_topic_formname,formsortvar=formname);*/

%xlsx_to_bookmarks_by_topic(input_xlsx=D:\Users\fan\Desktop\program studyid\bookmarker\bookmarker, 
output_ps=D:\Users\fan\Desktop\program studyid\bookmarker\bm_by_topic_pagenum,formsortvar=pagenum);

````



![../../Z appendix/699QC29H.png|undefined](/img/user/Z%20appendix/699QC29H.png)

### 3.3. gswin64c软件加标签

通过gswin64c.exe，将ps配置文件导入到acrf(没有标签)上

#### 3.3.1. 一般无法添加环境变量
需要把，要加标签的acrf和ps配置文件放在bin文件夹下，和gswin64c.exe同级别，再在此处运行CMD，再运行代码即可
- ![../../Z appendix/Pasted image 20231016105819.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020231016105819.png) 

- `cd 'D:\software\bookmarker\gs10.01.2\bin'`
- `gswin64c -o acrf_out.pdf -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress acrf.pdf bm_by_topic_pagenum.ps`
- `gswin64c -o acrf_out1.pdf -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress acrf_out.pdf bm_by_visit_pagenum.ps`
- 

#### 3.3.2. 可以添加环境变量
也可以按照如下方式，可为软件添加环境变量，把文件放在任意地方，都可以运行软件。如把相关文件放在'D:\Users\fan\Desktop\program studyid\bookmarker'位置：
- ![../../Z appendix/BBRJN8Y8.png|undefined](/img/user/Z%20appendix/BBRJN8Y8.png)

- cmd运行代码：
	- `cd 'D:\Users\fan\Desktop\program studyid\bookmarker'`
	- `gswin64c -o acrf_out.pdf -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress acrf.pdf bm_by_topic_pagenum.ps`
	- `gswin64c -o acrf_out1.pdf -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress acrf_out.pdf bm_by_visit_pagenum.ps`
	- 

### 3.4. 
*   通过PDFXEdit.exe，在最前面根据标签添加目录(table of content)

## 4. 🔚 结论

最后输出样式这样：

![../../Z appendix/CRI6JTT6.png|undefined](/img/user/Z%20appendix/CRI6JTT6.png)

