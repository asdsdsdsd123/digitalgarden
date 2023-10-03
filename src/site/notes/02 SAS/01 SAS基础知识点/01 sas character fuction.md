---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/01 sas character fuction/","dgPassFrontmatter":true}
---


## 1. 大小写

### 1.1. lowcase upcase propcase

```ad-note
- lowcase：英文字符全转小写，var_low=lowcase(var);
- upcase：英文字符全转大写，var_hi=upcase(var);
- propcase英文字符首字母大写，var_prop=propcase(var);
```

## 2. 去掉空格

### 2.1. left/trim/strip/cats/trimn/compbl

#### 2.1.1. `多种函数去掉不同部分的空格`

- left：去掉前置空格;
- trim: 去掉后面的空格
- strip：去掉前后的空格
- strip(var)=left(trim(var))；
- cats(var)=strip(var)，`不过不提示数值转字符的WARNING`

![../../Z appendix/Pasted image 20221012124843.png](/img/user/Z%20appendix/Pasted%20image%2020221012124843.png)  

#### 2.1.2. 与空格相关的注意事项：

- x="a"; y=" a"; `x不等于y`
- x="a"; y="a "; `x等于y`

- trim/trimn/compbl
	- trim：去掉尾部空格,""-->" "
	- trimn：同去掉尾部空格，""-->""
	- compbl：将连续2个以上空格转为1个空格

```sas
data t; 
	a="A"||trim("")||"B" ;/*A B*/ 
	b="A"||trimn("")||"B" ;/*AB*/ 
	c="A"||compbl("B M A");/*AB M A*/
run;
```
{ #3391bf}


## 3. 等返回字符长度

### 3.1. length lengthn %length

> [!note] length lengtn %length
> - length
> 	- 返回string（去除尾部空格）的字符数。
> 	- `对于string=""/" "，返回1`
> - lengthn
> 	- 返回最右边非空字符的位置。
> 	-  `对于string=""/" "，返回0`
> - %length
> 	- 可用于text-string和宏变量。字符串直接数长度，宏变量解析后，解析值数字符数
> 	- `对于宏变量解析为空的，返回1`

![../../Z appendix/Pasted image 20221010222747.png](/img/user/Z%20appendix/Pasted%20image%2020221010222747.png)  

## 4. 查找字符串

### 4.1. find/index verify

- find/index(var，string)，返回var中指定string出现的开始位置
	- 最基础用法一致，find(a,str)=index(a,str)
	- find可多加一个参数，指定从第几位开始查询，`index不可以`

```sas
	a="c/macro/tfl.sas"; 
	b=find(a,"/");    /*b=2*/ 
	c=index(a,"/");   /*c=2,这里index与前find一致*/
	d=find(a,'/',3);  /*d=8，指定第三个参数，从第3为开始查找*/ 
	e=find(a,'/',-200);    
	/*正数到200，然后倒数到最近的指定字符/，返回此时的正向编码，e=8*/
```
`注意以上的find(a,'/',-200)的用法`

- verify(var，string)，返回var中没有string中字母的开始位置（返回值可为0，此时string中指定的字母全在var中）`与前find/index用法正好相反`

```sas
var="abcd ";reslult=verify(var,"abcd");
			result2=verify(var,"abcd ")/*4 0*/

var="a a";reslult=verify(var,"abcd");
result2=verify(var,"abcd ")/*2 0*/

var="a m d";reslult=verify(var,"abcd");
result2=verify(var,"abcd ")/*2 3*/
```

### 4.2. anyalnum

- 返回第一个(非)字母/数字/特殊字符的位置
-`用法：anyalphanum(string，4)-->从第四位开始，返回遇到的第一个的字母或数字的位数

```ad-note
title: 其他同类函数 anyalnum/alpha/digit/punct,notalnum/alpha/digit/punct 

- anyalnum：寻找字符数值，返回符合的位置
	- notalnum ：非字符数值，返回符合的位置
- anyalpha：寻找字符，返回符合的位置
	- notalpha ：寻找非字符，返回符合的位置
- anydigit：寻找数值，返回符合的位置
	- notdigit ：寻找非数值，返回符合的位置
- anypunct ：寻找符号，返回符合的位置
	- notpunct ：寻找非符号，返回符合的位置
```

## 5. 去除、保留字符中某些部分

### 5.1. substr scan compress 

#### 5.1.1. substr scan
- substr(a,1,10)
	- substr(var,param1,param2)，从param1开始截取，截取长度为param2。param2长度不得大于var的length，否则报错。不指定param2，默认从param1截到字符串末尾

- scan(a,2,".")
	- scan(var,param1,param2)，`param2指定分隔符，`,截取分隔符的param1段。可不指定param2，使用默认分隔符空格逗号（都包含以上）等。
	- `可指定第二个字符为负号，则倒着截取：
![../../Z appendix/Pasted image 20221011162904.png](/img/user/Z%20appendix/Pasted%20image%2020221011162904.png)


#### 5.1.2. compress
 - compress (var，chars，modifiers)。chars区分大小写
 -  项目中这样用，在复杂文本，如保留数值+小数点：
	 - `Compress(var,"0123456789.","k")，等价于Compress(var,".","kd")`

 - 其他示例：
	- compress(var,"a")去掉字母“a”
	- compress(var,"","a")去掉所有字母，只保留数字=comress(var,"","kd")
	- compress(var,"a","d")去掉所有字母，去掉所有数字--没有modifier k则表示都删除
	- comress(var,"S","kd")保留S，保留所有数字

- 选项解析
- chars 指定一栏初始字符，默认它是要从source里移除的。如果指定"K"modifier，返回的结果则保存这些字符。	
- modifiers  指定一个修饰符，函数的具体功能。
	- 如：- 增加(A - Z, a - z)到初始字符里（chars）
		- d 增加数字到初始字符里（chars）
		- k 不移除初始字符（chars）而是返回这些字符
		- p 增加标点符号|
		- s 增加空格， 包括空格、水平制表符、垂直制表符、回车符、换行符和换页符。`少用，因为会把string中间的空格也会删掉`
		- l  增加小写字母（a - z)
		 - n 增加数字、下划线和字母(A - Z, a - z)
		- t 剪掉尾部空格
		- u 增加大写字母(A - Z)
		- w 增加可印刷的字符
		- X 增加十六进制字符
 -  [见网页](http://blog.sina.com.cn/s/blog_6e0a03730100mwv9.html)

#### 5.1.3. Ksubstr/Kscan/Kcompress

- 与不加K的函数用法基本一致，不过以字数为单位非字节。如一个汉字是3个字节，但是是1个汉字(或者算两个位数)。涉及中文string的，最好用K开头的函数处理，klenth同理。
 
![../../Z appendix/Pasted image 20221012235814.png](/img/user/Z%20appendix/Pasted%20image%2020221012235814.png)

如项目中，中文太长，需要200字符一拆分放，XXTERM--XXTERMn。

>  ![../../Z appendix/Pasted image 20221012234711.png](/img/user/Z%20appendix/Pasted%20image%2020221012234711.png)  
> ![../../Z appendix/Pasted image 20221128214728.png](/img/user/Z%20appendix/Pasted%20image%2020221128214728.png)

## 6. cat系列，连接文本

 > [!note]+ cat系列，cats与catx常用
> - cat(`of` var1`-`var4)=
> 	- var1||var2||var3||var4; 
> 	- cat不去除空格，单纯连接作用
> - catt(`of` var1-var4)=
> 	- trim(var1)||trim(var2)||trim(var3)||trim(var4);
> 	- catt只去除尾部空格，再连接
> - cats(`of` var1-var4)=
> 	- strip(var1)||strip(var2)||strip(var3)||strip(var4);
> 	- left(trim(var1))||...||left(trim(var4))；
> 	- cats前后空格都去掉，再连接。[[02 SAS/01 SAS基础知识点/01 sas character fuction#^3391bf\|01 sas character fuction#^3391bf]]
> 		- cats(a,b,c)是将a b c三个变量去掉前后空格一起拼接起来，即使有参数是空格，也会去掉该空格再拼接。一般多和||等一起使用
> - catx(`","`,`of` var1-var4)=
> 	- strip(var1)||","||strip(var1)||","||strip(var1)||","||….

## 7. count系列，对指定str计数

- count/countc/countw

![../../Z appendix/Pasted image 20221012224332.png](/img/user/Z%20appendix/Pasted%20image%2020221012224332.png)
### 7.1. count及countc

- 用法：count/countc(var,"str",modifier)。
	- count计算var中指定字符str出现的次数
	- countc计算var中指定字符str以字母为单位，在var中指定字母出现的次数
	- modifier：i，忽略大小写；t，忽略尾部空格

```sas
data test;
	 a="bm c Bm";
	 
	 b=count(a," bm","it"); 
	 /*查找空格b或空格B，指定字符前有空格，找到1个*/
	 c=count(a,"bm ","it");
	 /*查找b或B，虽然指定了尾部空格，t把它裁剪了，整体忽略大小写，找到2个*/

	d=countc(a," bm","it") /*指定的字母空格 大写小写bm，var中有6个字母满足*/
	e=countc(a,"bm ","it") /*t先去除了空格，指定字母大写小写bm，var中4个字母满足*/
run;
```


### 7.2. countw

- 用法：countw(var,"分隔符")，计算分隔符下var中，可分为几部分
	- countw(var)=countw(var," ")，默认按照空格逗号等【空格!$%&()✳+,-./;<^|】，一起作为分隔符分隔计数。
	- count(var,", ")，指定按照", "分隔。
 

 ```sas
data a;
	string="a b ab, ab";
	x2=countw(string); /*4, 等于countw(string," ")*/
	x3=countw(string,", ")/*2*/
run;
 ```


 ![../../Z appendix/Pasted image 20221012225105.png](/img/user/Z%20appendix/Pasted%20image%2020221012225105.png)

## 8. translate与tranwrd，替换文本

- transwrd(var, "var-str","spec-str")
> ![../../Z appendix/Pasted image 20221012225842.png](/img/user/Z%20appendix/Pasted%20image%2020221012225842.png)

- translate(var,"spec-str","var-str")

> ![../../Z appendix/Pasted image 20221012225811.png](/img/user/Z%20appendix/Pasted%20image%2020221012225811.png)

### 8.1. 项目中应用

- 应用扩展，如去除项目中的特殊符号：换行符/Tab符号等，保留文本中间的空格
- 以下是将这些特殊符号抓为了空格
	- strip(tranwrd(tranwrd(QVAL,'0A'x,' '),'0D'x,' '));-->
	- strip(compbl(translatel(QVAL," '', '090a0da0'x)));
	- 09（tab）0a(回车) 0d（换行）

- 这些'0A'x及'0D'x为hex的写法，项目中遇到的不明特殊符号，可put hex再去除
	- put hex：如把数据中的不明符号复制过来(放到xx变量)，再 put xx hex，看日志的十六进制码

## 9. 其他字符函数

### 9.1. coalesce(s)，选择最近的非空值

- 回数值（coalesce）/字符(coalescec)变量的第一个非空的value
	- y=coalesces（x1，x2，x3），依次查找x1，x2，x3的值，将其非缺失值value赋予y
- 如，令bc=ac，若缺失ac，赋值bc为"missing"：bc=coalescec(ac,“missng”)

### 9.2. 正则表达式

 - 简单示例
> ![../../Z appendix/Pasted image 20221016185844.png](/img/user/Z%20appendix/Pasted%20image%2020221016185844.png) 

- 详细版 
> ![../../Z appendix/Pasted image 20221016185850.png](/img/user/Z%20appendix/Pasted%20image%2020221016185850.png)

#### 9.2.1. 示例

```ad-note
collapse: open
title: 

- 写法：prxmatch("/正则表达式/",varname)
	- 正则表达式，匹配`字符串`："/`第\d次给药|筛选期`/"。字符串包含筛选期，或包含第n次给药
	- 正则表达式，匹配`任一字符`："/`[第\d次给药|筛选期]`/"。字符串包含筛/选/期/第/n/次/给/药
- 注：大致用法如此，具体怎么匹配的参考可查询相关资料

```

### 9.3. repeat与reverse，文本重复

- repeat("ONE",2)-----ONEONE
- reverse("xyz")-------zyx


## 10. put显示特殊字符

put var hex.  一个numeric put两位0'X

![../../Z appendix/Pasted image 20230227160907.png](/img/user/Z%20appendix/Pasted%20image%2020230227160907.png)

````ad-light-green

SAS代码去除字符的特殊字符值

```sas
%macro dck_special_char(_insds=, _outsds=, comp=);

%if %length(&_outsds)=0 %then %do;
%let _outsds=&_insds;
%end;

data &_outsds;
set &_insds;
array _v[*] _character_;
do ii = 1 to dim(_v);
_v[ii]=strip(compbl(translate(_v[ii], ' ', '090a0d'x)));

end;
drop ii;
run;

%if %upcase(&comp)=%str(Y)
 %then %do;
proc compare b=&_insds c=&_outsds listall maxprint=(30, 32000) note;
run;
%end;

%mend dck_special_char;
```
````


