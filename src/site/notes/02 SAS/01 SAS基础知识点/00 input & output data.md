---
{"dg-publish":true,"dg-permalink":null,"permalink":"/02 SAS/01 SAS基础知识点/00 input & output data/","dgPassFrontmatter":true}
---


## 1. column/list/format input

- column input与list input可只有这两种连用
	- input city $ 18-24 name $;

- column input与format input也可只有这两种连用
	- input city $ 18-24 name $20.;
	- input name $12. date date7. +2 amount;

- 只有list input和format input，❌
	- input name $12. date date7. amount; ❌
	- input city $ name $20.;❌

- 三种的可以混用

 
## 2. 常用日期相关format

|  日期                           |  时间                                  |  日期时间                                      |
|:------------------------------|:-------------------------------------|:-------------------------------------------|
|  `yymmdd10`.</br>2021-07-09     |  time5. `6:14`</br>time8.,`9:06:14`  |  datetime20.</br>(`01JAN1960:`06:14:30)    |
|  `is`8601`da`.<br>2021-07-09  | `tod`5. `06:14`<br>tod8.,`09:06:14`  |  `is`8601`dt`.<br>(`1960-01-01T`06:14:30)  |
| `除此之外还有数值的z5.-->00001`     |                                      |                                            |  

## 3. 数据集/数据变量属性

```sas
/*去掉变量的format informat: */
format/informat _all_

/*为数据集加label*/
data xx(label="xxxxx")；
	...
	label var1 "xxx";
run;
```


### 3.1. `名称，长度等属性限制`
{ #saslength}


- 数据集/变量名字限制
	- 字母或下划线开头
	- 不限大小写
	- 只能有字母，数字或下划线
	- 1-32个长度（`CDISC变量8个长度限制`）

- 数值长度限制
	- 缺失：（字符=""，数值="."）。有用.a-z/.A-Z表示比.更小的缺失
	- 字符储存长度，32767，默认8 character
	- 数值存储长度，3-8 byte，默认8 byte，大概16位数字

- format名称
	- 名称长度，字符的format 31个长度，数值的format 32个长度
	- 名称，不能以数字开头结尾。
	- 不能是SAS默认的format名称（如date, yymmdd, month等）

- format label长度：最长32765
{ #76a5bf}



---

```sas
/*设定逻辑库只可读，不可写*/
libname raw "D:\raw" access=readonly;
```

## 4. 创建SAS数据集

### 4.1. set语句

- set语句创造数据，可加入选项
	- end=var(最后一条观测var变量为1) 
	- nobs=x（x值为所有数据集的观测数）
	- point=（直接读入第几条观测）
	- firstobs=n1 obs=n2(读入n1-n2间的数据)

### 4.2. `infile+input方式创造dummy数据`

- 先infile再input，card产生dummy数据。看情况加入适当选项
	- infile的missover truncover dsd dlm=','等选项，
	- input的`@` `@@` + \#等修饰符（少用？）
	- card产生dummy的数据

```sas
data dummy;
	length col $200;
	infile cards delimiter=",";
	/*默认分隔符是空格，数据中有空格，需换其他数据中不存在的分隔符*/
	input grp ord col $200;
cards;
1,1,所有TEAE
1,2,与BCMA T细胞相关的TEAE
2,1,与预处理/清淋相关的TEAE
2,2,CTCAE3-5级的TEAE
;	
```

### 4.3. proc import

- [[02 SAS/01 SAS基础知识点/04 Proc step#1.3 proc import\|04 Proc step#1.3 proc import]]

## 5. SAS数据集导出外部文件

### 5.1. file&put

- 可file+put导出：
```sas
filename fmt_out "&pathname\fmt input.txt";

data _nul1_;
	set col_input; 
	file fmt_out;
	put @1 name @14 date @23 amount dollar6.;
	/*一个put，表示txt的一行*/
run;
```
- `也可put多行`（3行，一条观测，Put外部数据三行-tricker/price/inductry）：
![../../Z appendix/Pasted image 20221007183108.png](/img/user/Z%20appendix/Pasted%20image%2020221007183108.png)
- [[02 SAS/01 SAS基础知识点/03 sas statement#1 3 put，输出value到log\|03 sas statement#1 3 put，输出value到log]]

### 5.2. proc export
-  [[02 SAS/01 SAS基础知识点/04 Proc step#1.2 proc export\|04 Proc step#1.2 proc export]]










