---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/04 Proc step/","dgPassFrontmatter":true}
---


## 1. proc compare

> [!note] proc compare基本用法
> - 也可加上out=xxx，将list结果输出到sas数据集，`一般少用`
> - 也可加`id idnum`；id语句指定根据哪个变量匹配，不加的话默认相同观测匹配。需要保证每个观测的idnum值相同
> 	- `id usubjid rscat rseval rstestcd rsdtc rsorres;此时按照列举的by变量进行匹配`
> - ![../../Z appendix/Pasted image 20221013233813.png](/img/user/Z%20appendix/Pasted%20image%2020221013233813.png) 
> - ![../../Z appendix/Pasted image 20221013233905.png](/img/user/Z%20appendix/Pasted%20image%2020221013233905.png)
> 

## 2. proc export

> [!note] proc export
> - proc export data=sashelp.class outfile="c:\sas\class.xlsx" dbms=xlsx label replace;
> 	- label指，输出到excel中的列名称为数据集变量的标签而非变量名
> - `项目中的：`
> - ![../../Z appendix/Pasted image 20230125160332.png](/img/user/Z%20appendix/Pasted%20image%2020230125160332.png)
> - ![../../Z appendix/Pasted image 20230125160731.png](/img/user/Z%20appendix/Pasted%20image%2020230125160731.png)




## 3. proc import

> [!proc import]
> -  proc import `datafile`="c:\sas\class.xlsx" `out`=class `dbms`=xlsx `replace`;
> 	- getnames=yes;  `默认为yes，excel第一列作为数据集列名`
> 	- sheet=" ";  `默认导入为第一个sheet,可指定导入sheet`
> 	- range="sheetname$A1:C20";   `第一行作为变量名了，因此读到第19行`
> 	- mixed=no;   `默认no。改为yes后，将字母数值混合列当成字符处理`
> 	- datarow=n;   `从第n行开始读取数据`
> 	- guessingrows=n;  `从前n行确定变量类型`
> 	- scantext=Yes;  `默认no，变量读到256长度。Yes，变量自动读完`
> 	- usedate=yes;  `excel中日期以数值读取`
> 	- textsize=n;   `定义变量读取长度,n数值在1-32767之间`
> 	- delimiters=" ";  `指定分隔符`

## 4. proc format

>[!proc format] 
>> [!value invalue，定义format格式]
>> - value定义的format看等号左侧，`格式名称可以加$(等号左侧字符型)，也可以不加$（等号左侧是数值型）`，`value定义的format和put一起用`
>> - invalue定义的format看等号右侧，`格式名称不加$（等号右侧是数值型）`，`invalue定义的format和input一起用`
>> - Invalue 可定义形如“a”=1类格式，字符转化数字的。value虽也可定义，但是最后结果是字符（因为用的时候是put的）
>> - ![../../Z appendix/Pasted image 20221014231433.png](/img/user/Z%20appendix/Pasted%20image%2020221014231433.png)
>
>> [!与数据集或html交互，输出format格式]
>> 
>> - 将raw逻辑库下format内容输出到output html
>> 	- Proc fomrat `lib=raw fmtlib`; run;
>> -  将raw逻辑库下format内容输出到指定数据集
>> 	- Proc format `lib=raw cntlout=data-set`; run;
>> - 将in逻辑库下cntlfmt数据集输入成formt，并存到raw逻辑下
>> 	- proc format `lib=raw cntlin=in.cntlfmt`; run;
>> 		- ![../../Z appendix/Pasted image 20221016213605.png](/img/user/Z%20appendix/Pasted%20image%2020221016213605.png)  ![../../Z appendix/Pasted image 20221016213551.png](/img/user/Z%20appendix/Pasted%20image%2020221016213551.png)  ![../../Z appendix/Pasted image 20221016213605.png](/img/user/Z%20appendix/Pasted%20image%2020221016213605.png)
> 
>> [!将format catalog存放其他位置（默认work）及后续读取]
>> - 将format catalog存放其他位置
>> 	 - proc format `library=libname`;
>> 	 - value xxx;
>> - 下次使用时，可读取已经保存在libname中的format
>> 	- option `fmtsearch`(`libname`);
>

> [!项目中的100以100显示，其余为小数点后一位]
> - 先设定format
> 	- ![../../Z appendix/Pasted image 20221016214615.png](/img/user/Z%20appendix/Pasted%20image%2020221016214615.png)
> 
> - 再应用
> 	- col2=cats(n)||" ("||cats(put(n/tot\*100,`pct.`),")");

- ---------------------------
- ![../../Z appendix/Pasted image 20230106171805.png](/img/user/Z%20appendix/Pasted%20image%2020230106171805.png)

- ![../../Z appendix/Pasted image 20230106171819.png](/img/user/Z%20appendix/Pasted%20image%2020230106171819.png)

```sas
proc format;
	picture perct (round fuzz=0)
		0     =    '  '   (noedit)
		0< - <10 =  '(9.9)' (prefix='(')
		10 - <100 = '(99.9)' (prefix='(')
		100 = '(100)' (noedit)
		other = '(>100)' (noedit);

```


## 5. proc sort

> [!proc sort基本用法] 
> - proc sort `nodupkey`;`by _all_`;  -->  proc sort `nodup；默认是对所有变量的去重`
> 	- ![../../Z appendix/Pasted image 20221014231824.png](/img/user/Z%20appendix/Pasted%20image%2020221014231824.png)
> - 加选项`汉字按拼音排序`：
> 	- ![../../Z appendix/Pasted image 20221014232248.png](/img/user/Z%20appendix/Pasted%20image%2020221014232248.png)

## 6. proc datasets

> [!proc datasets]
> - `删除数据集，nolist不展示信息`
> 	- proc datasets library=work `memtype=data` `nodetails nolist` `kill` `nowarn`;
> 	- ![../../Z appendix/Pasted image 20221014232933.png](/img/user/Z%20appendix/Pasted%20image%2020221014232933.png)
> - `删除数据集/(format macro catalog)信息`
> 	- proc datasets library=work `mt=(data catalog)` nolist kill;
> 	- ![../../Z appendix/Pasted image 20221014232944.png](/img/user/Z%20appendix/Pasted%20image%2020221014232944.png)
> - `仅保留（删除）某些数据集`
> 	- proc datasets lib=work memtype=data nolist nodetails nowarn `delete/save` `ds-name`; 
> 	- ![../../Z appendix/Pasted image 20221014232954.png](/img/user/Z%20appendix/Pasted%20image%2020221014232954.png)
> - 改变给定数据集名称 
> 	- ![../../Z appendix/Pasted image 20221014233004.png](/img/user/Z%20appendix/Pasted%20image%2020221014233004.png)
> - 改变给定数据集属性（数据集标签，变量标签，加format ） 
> 	- ![../../Z appendix/Pasted image 20221014233015.png](/img/user/Z%20appendix/Pasted%20image%2020221014233015.png)

## 7. proc copy

> [!proc copy]
> 
>> [!基本写法]
>> - proc copy `in=raw out=work memtype=data`;
>> - `select /*exclude*/`  dsname;
>> - run;
>
>> [!输出到xpt格式]-
>> - libname `outxpt` `xport` "c:\cuifan\class.xpt";
>> - proc copy in=sashelp out=`outxpt` `mt`=data;select class;run;

## 8. proc freq 

> [!proc freq]
> 
>> [!proc freq基本用法]
>> - table `type*order`/out=freq1;
>> ----------------------------
>> - `by type`;
>> 	- table `order`/out=freq1;
>> ---------------------------
>> - 以上两种算出来的type\*order的count一样
>> 	- ![../../Z appendix/Pasted image 20221015102604.png](/img/user/Z%20appendix/Pasted%20image%2020221015102604.png)
>
>> [!proc freq中table选项]
>> - 加missing选项，将missing当成一类值计算总数
>> - tables agegr\*sex/`missing`
>> 	- ![../../Z appendix/Pasted image 20221015113045.png](/img/user/Z%20appendix/Pasted%20image%2020221015113045.png)
>> 	- 上图ods output输出统计量到sas数据集，关键字可在ods trace on得到
>> - 其他卡方相关统计选项见其他章节
>

## 9. proc transpose

> [!proc transpose]
>> [!纵向转横向，把variable数值转到变量名称上]
>> - proc transpose data=a out=bb `prefix=trt suffix=aa`;
>> 	- by patient;
>> 	- var value;
>> 	- id visit;
>> 	- ![../../Z appendix/Pasted image 20221015104204.png](/img/user/Z%20appendix/Pasted%20image%2020221015104204.png)
>> 
>>  ----------------------------
>> 	- id visitnum; 
>> 	- idlabel visit; `/*这里把visitnum值放到变量名，将对于visit赋予变量标签*/`
>> 	- ![../../Z appendix/Pasted image 20221015105322.png](/img/user/Z%20appendix/Pasted%20image%2020221015105322.png)
>
>> [!横向转纵向，把多个变量放到某一变量的值]
>> -  proc transpose data=a out=b `prefix=value;`
>> 	- by subjid visitnum;     `/*keep这些变量，未列出的不保留*/`
>> 	- var value1-value3;
>> 	- ![../../Z appendix/Pasted image 20221015111103.png](/img/user/Z%20appendix/Pasted%20image%2020221015111103.png)

## 10. proc means

- Proc means指定分类变量可用by，也可用class（`需在proc means 加nway选项）；可计算stderr。`

## 11. proc univariate

- Proc univariate可以进行样本正态性检验，但`指定分类变量时只能用by`

> [!proc mean与proc univariate做基线的统计量]
> 
> ![../../Z appendix/Pasted image 20221017231127.png](/img/user/Z%20appendix/Pasted%20image%2020221017231127.png) ![../../Z appendix/Pasted image 20221017231201.png](/img/user/Z%20appendix/Pasted%20image%2020221017231201.png)

## 12. proc report

- [[02 SAS/01 SAS基础知识点/05 Proc report及ods style设置\|很重要，后面文档单独列出]]




