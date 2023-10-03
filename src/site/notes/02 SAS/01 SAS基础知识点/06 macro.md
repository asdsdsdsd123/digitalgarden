---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/06 macro/","dgPassFrontmatter":true}
---


## 1. 自动宏变量automatic variable

- %put \_automatic_，输出自动宏变量
> [!sas中的自动宏变量]
> ![../../Z appendix/Pasted image 20221016105621.png](/img/user/Z%20appendix/Pasted%20image%2020221016105621.png)

> [!sql select 语句运行后，产生sqlobs宏变量]
> - sql select执行后产生sqlobs宏变量
> 	- If `an output table is created`, then `SQLOBS contains the number of rows in the output table (同DATA步中自动宏变量，&sysnobs)`
> 		- If `a single macro variable is created`, then SQLOBS contains the value `1`.
> 		- If a `macro variable list or macro variable range` is created, then SQLOBS contains the` number of rows that are processed to create` the macro variable list or range
> 		- If `no output table, macro variable list, or macro variable range` is created, then SQLOBS contains the value `1`
> 		- If an `SQL view is created`, then SQLOBS contains the value `0`

## 2. 创建宏变量

- 创建的宏变量名称，要求：
	- `32长度以内`
	- `不要以AF/DMS/SQL/SYS开头`，这些一般用于系统的自动宏变量
	- 用的时候`大小写不敏感`

> [!data步call symput/x 创建宏变量，将data里的值赋予宏变量]
> 
> call symput与call symputx区别在于，call symputx返回的宏变量值去除了尾部空格。
> - call symputx(''宏变量名字'',值);
> 	- `通过一个data步，创建一个宏变量，data步多次迭代，最后宏变量值为最后一次迭代对应的值` #注意事项/多个值创建一个宏变量，选取其中一个 
> 	- `通过一个data步，创建多个宏变量`，每次迭代，创建不同的宏变量
> 
>> [!通过一个data 步，创建多个宏变量]
>> 
>> ![../../Z appendix/Pasted image 20221016112010.png](/img/user/Z%20appendix/Pasted%20image%2020221016112010.png)
>
> - 默认，在data步执行完才会产生宏变量，即不能在该data 步，使用该data步产生的宏变量。若`在该data步使用同data步创建的宏变量：
> 	- Symget('没有&')
> 	- Resolve('有&')，注意用''，否则会解析&，而这里不能解析
> 
>> [!在同一data步使用该data步产生的宏变量]
>> 
>> - 两种方法：
>> 	 - ![../../Z appendix/Pasted image 20221016112847.png](/img/user/Z%20appendix/Pasted%20image%2020221016112847.png)  ![../../Z appendix/Pasted image 20221016112907.png](/img/user/Z%20appendix/Pasted%20image%2020221016112907.png)  ![../../Z appendix/Pasted image 20221016113114.png](/img/user/Z%20appendix/Pasted%20image%2020221016113114.png)
>>  - 项目中的使用：
>> 	 - ![../../Z appendix/Pasted image 20221016113229.png](/img/user/Z%20appendix/Pasted%20image%2020221016113229.png)

> [!proc sql步创建宏变量]
>
> - 创建一个宏变量name1, 若存在多个name值，宏变量值为第一个name的值。  #注意事项/多个值创建一个宏变量，选取其中一个 
> 	-  select distinct name into :name1 `trimmed` 
> 
> -  创建一个宏变量name1, 若存在多个name值，用分隔符-连接起来
> 	- select distinct name into : name1 `separated by '-'`
> 
> - 把一个name变量的多个值放到多个macro变量里(name1 name2 name3)
> 	-  select distinct name into :name1`-`   `这里每个name1 name2等都trimmed`
> 
> - 一个select语句可以根据不同变量（如name height），生成不同宏变量
> 	- select `distinct name，height` `into :namel-，:height1-`
> 	- select `distinct name, height into :name separated by " ", :height separated by " " `
> 	
>> [!图中如上知识点]
>> 
>> ![../../Z appendix/Pasted image 20221016120047.png](/img/user/Z%20appendix/Pasted%20image%2020221016120047.png)
> 
>> [!项目中sas代码]
>> ```sas 
>> proc sql noprint;
>> 	select sum(trt01an=1 and pdfl='Y' and phasecat="&phase.")`,`
>> 		sum(trt01an=2 and pdfl='Y' and phasecat="&phase."),
>> 		sum(trt01an=3 and pdfl='Y' and phasecat="&phase."),
>> 		sum(trt01an=4 and pdfl='Y' and phasecat="&phase."),
>> 		sum(trt01an=5 and pdfl='Y' and phasecat="&phase."),
>> 		sum(trt01an=6 and pdfl='Y' and phasecat="&phase.")
>> 			`into :`&phase.pop1 trimmed`,`
>> 				 `:&`phase.pop2 trimmed,
>> 				 :&phase.pop3 trimmed,
>> 				 :&phase.pop4 trimmed,
>> 				 :&phase.pop5 trimmed,
>> 				 :&phase.pop6 trimmed
>> 	 from adam.adsl(where=(pdfl='Y' and phasecat="&phase."));
>> 	 
>> 	 select sum(trt01an=99 and pdfl='Y' and phasecat="&phase.") into :&phase.pop99 trimmed
>> 	 from adam.adsl;
>> quit;
>> ```	 
>

## 3. 去掉宏变量值中的空格

- %let再定义
- call symputx定义
- proc sql 加trimmed
- %cmpress

> [!示例如图]
> ![../../Z appendix/Pasted image 20221016121026.png](/img/user/Z%20appendix/Pasted%20image%2020221016121026.png)

## 4. 宏的option

> [!option]
> 在%macro指定的option权限高于option语句指定的 #注意事项/属性范围 
>> [!可在%macro中写的option,%macro name/option]
>>
>> ![../../Z appendix/Pasted image 20221016122455.png](/img/user/Z%20appendix/Pasted%20image%2020221016122455.png)
>
>> [!在option语句指定宏相关的option]
>> - 与宏存储相关的
>> 	- ![../../Z appendix/Pasted image 20221016122726.png](/img/user/Z%20appendix/Pasted%20image%2020221016122726.png)
>>
>> - minoperator，产生in的作用
>> 	- ![../../Z appendix/Pasted image 20221016123136.png](/img/user/Z%20appendix/Pasted%20image%2020221016123136.png)
>>
>>> [!in operator的sas code]
>>> ```sas code
>>> option minoperator;
>>> 
>>> %macro test(names=);
>>> 
>>> %if %upcase(&names) in ALICE JAMES JOHNJUDY %then %do;
>>> 	%put &names exists in the list;
>>> %end;
>>> 
>>> %mend;
>>> 
>>> %test(names=%nrstr(alice))
>>> ```
>
>

## 5. 宏的存储及调用

> [!写宏的时候，可以指定加密宏程序]
> ![../../Z appendix/Pasted image 20221016154506.png](/img/user/Z%20appendix/Pasted%20image%2020221016154506.png)
> 

### 5.1. %incluse读入宏程序

> [!%incluse读入宏程序]
> 
> - %inc "&\_rootpath.\macros\get_rtf_outname.sas";
> 	- *之前的宏程序保存放到指定路径（macros），保存的程序名字与宏名字（get_rtf_outname）保持一致, %inc读入宏程序，就可以用了%get_rtf_outname。*;
> 
> - ![../../Z appendix/Pasted image 20221016184825.png](/img/user/Z%20appendix/Pasted%20image%2020221016184825.png)
> 	- 上图中.\表示路径的相对引用，是同sas程序的文件夹位置
> 

### 5.2. Autocall macro facility

- Autocall macro facility
	- options `mautosource` `sasautos=("library-specification-1"...)`

> [!Autocall macro facility使用]
> - 定义后存储宏（保存.sas程序）：
> 	- ![../../Z appendix/Pasted image 20221016153041.png](/img/user/Z%20appendix/Pasted%20image%2020221016153041.png)
> 
> - 调用宏
> 	- ![../../Z appendix/Pasted image 20221016153122.png](/img/user/Z%20appendix/Pasted%20image%2020221016153122.png)  ![../../Z appendix/Pasted image 20221016153241.png](/img/user/Z%20appendix/Pasted%20image%2020221016153241.png)
> 

### 5.3. Stored compiled macro facility

- Stored compiled macro facility
	- libname `mylib` 'SAS-data-library';
	- options `mstored` `sasmstore`=`mylib`;

> [! Stored compiled macro facility使用]
> - 定义后存储宏：
> 	- ![../../Z appendix/Pasted image 20221016152127.png](/img/user/Z%20appendix/Pasted%20image%2020221016152127.png)
> 
> - 调用宏
> 	- ![../../Z appendix/Pasted image 20221016152148.png](/img/user/Z%20appendix/Pasted%20image%2020221016152148.png)

### 5.4. 多个&的解析

> [!多个&&的解析]
> 
> &&会解析为一个&，由左到右依次解析
> ![../../Z appendix/Pasted image 20221016163246.png](/img/user/Z%20appendix/Pasted%20image%2020221016163246.png)

## 6. 宏函数

### 6.1. %symexist判断指定宏变量是否存在

> [!%symexist 判断指定宏变量是否存在]
> - %symexist (macro-variable-name) -->若宏变量存在返回1，否则返回0
> 	- 用法：
> 		- ![../../Z appendix/Pasted image 20221016160947.png](/img/user/Z%20appendix/Pasted%20image%2020221016160947.png)

### 6.2. 常用宏函数

> [!%evalf和%sysevalf函数]
> - 使宏的文本，可以进行数值运算，而非仅显示文本
> 	- %eval，计算的带小数点的，只会保留整数部分
> 	- %sysevalf，计算的带小数点的，也会有小数部分
> 		- ![../../Z appendix/Pasted image 20221016161808.png](/img/user/Z%20appendix/Pasted%20image%2020221016161808.png)

> [!%sysfunc可对宏变量使用date步函数。或直接使用宏函数]
> - 用法：%sysfunc(sas-data step-function)
> 	- 如：%sysfunc(substr(mvar,2,5))
> - 有的宏函数如%substr/%scan/%compress等可以直接用
> - 宏函数与SAS函数应用不同
> 	- ![../../Z appendix/Pasted image 20221016162655.png](/img/user/Z%20appendix/Pasted%20image%2020221016162655.png)
> 	
> 	- 正常sas函数里，参数带引号的，用宏里面要把引号去掉
> 		- %if %sysfunc(compress(&out,,kd))= %then %do
> 			- sas data set函数：compress(&out,,"kd")

> [!宏的正则用法，prxmatch]
> - %let regex_id=%sysfunc(prxparse(/\d/));
> - %put %sysfunc(prxmatch(&regex_id,123));

### 6.3. mask函数

> [!%str %nrstr与%bquote %nrbquote]
> - %str %nrstr是在`编译时mask`，`先mask，再解析`，
> 	- 注意此时mask了特殊字符（如分号 逗号等），只作为字符而没有特殊功能。
> 	- `nr`表明`多mask了 % &`。
> 	- 注意`单引号不配对`情况
> 
> - bquote %nrbquote是`在执行后，对结果mask`
> 	- `nr同前`，多mask了 % &
> 	- `单引号可不配对`
> 	
>> [!两种mask区别示例及应用]
>> 
>> ![../../Z appendix/Pasted image 20221016164953.png](/img/user/Z%20appendix/Pasted%20image%2020221016164953.png)  ![../../Z appendix/Pasted image 20221016165840.png](/img/user/Z%20appendix/Pasted%20image%2020221016165840.png) 	
>

> [!%qsubstr或%qscan等q开头的宏函数]
> - 在%substr或%scan中，mask不起作用，即使使用mask函数
> 	- `%qsubstr，对结果mask`（此时`mask了特殊字符`，只作为字符而没有特殊功能），`再substr`
> 	- ![../../Z appendix/Pasted image 20221016165709.png](/img/user/Z%20appendix/Pasted%20image%2020221016165709.png)  ![../../Z appendix/Pasted image 20221016165720.png](/img/user/Z%20appendix/Pasted%20image%2020221016165720.png)

> [!superq执行时mask，若结果有&，也只解一次]
> ![../../Z appendix/Pasted image 20221016171714.png](/img/user/Z%20appendix/Pasted%20image%2020221016171714.png)  ![../../Z appendix/Pasted image 20221016171848.png](/img/user/Z%20appendix/Pasted%20image%2020221016171848.png)

> [!unmask函数：%unquote()，unmask所有内容]
> - During macro execution, `unmasks all special characters and mnemonic operators` for a value
> 	- 一般在宏里这么写：
> 		- ![../../Z appendix/Pasted image 20221016172518.png](/img/user/Z%20appendix/Pasted%20image%2020221016172518.png)
> 

## 7. 宏的scope

- 分为两类，全局宏变量和局部宏变量。
	- 全局宏变量（可用%global mvar 定义）在整个sas程序中有效，
	- 局部宏变量（可用%local mvar 定义）只在该宏内生效

> [!简单介绍]
> ![../../Z appendix/Pasted image 20221016173848.png](/img/user/Z%20appendix/Pasted%20image%2020221016173848.png)

### 7.1. 宏参数的scope
- `%macro xxx（test）中的宏参数`，`自动创建为局部宏变量`（相当%local test），`在xx宏里%let再赋值test，改的值也是local test的值`。

> [!示例如图]
> ![../../Z appendix/Pasted image 20221016174355.png](/img/user/Z%20appendix/Pasted%20image%2020221016174355.png)
> 

### 7.2. 同名宏变量，未定义scope
- 对于`同名称宏变量`，若也`没有用%local %global定义scope`，其`scope由最开始定义`，其`值在最后决定（一层层值覆盖）`

> [!示例如图]
> ![../../Z appendix/Pasted image 20221016175503.png](/img/user/Z%20appendix/Pasted%20image%2020221016175503.png)   ![../../Z appendix/Pasted image 20221016175513.png](/img/user/Z%20appendix/Pasted%20image%2020221016175513.png)

### 7.3. call symput产生宏变量的特殊情况

- call symput函数创建宏变量，若`之前没有产生过局部宏变量，则没有local symbol`，则call symput`创建的宏变量是全局宏变量` （图1）
	- 若是`之前有过局部宏变量`，`有local symbol`，则call symput`创建的宏变量是局部宏变量` （图2）
	- 同前规则，1.7.2，同名的属性由开始定义，值由后面定义（图3）

> [!相关图片知识点如下]
> - ![../../Z appendix/Pasted image 20221016180007.png](/img/user/Z%20appendix/Pasted%20image%2020221016180007.png)  ![../../Z appendix/Pasted image 20221016180015.png](/img/user/Z%20appendix/Pasted%20image%2020221016180015.png)  
> - ![../../Z appendix/Pasted image 20221016180023.png](/img/user/Z%20appendix/Pasted%20image%2020221016180023.png)  ![../../Z appendix/Pasted image 20221016180031.png](/img/user/Z%20appendix/Pasted%20image%2020221016180031.png)

## 8. 宏程序返回0 1

### 8.1. 判断某数据集中是否有某个变量

> [! 判断某数据集中是否有某个变量]
> - 通过SAS函数varnum，open/close，判断该数据集在不在
> - 程序如图：
> 	- ![../../Z appendix/Pasted image 20221016181434.png](/img/user/Z%20appendix/Pasted%20image%2020221016181434.png)

### 8.2. 判断数据集是否存在

> [!判断数据集是否存在]
> - 程序
> 	- ![../../Z appendix/Pasted image 20221016181643.png](/img/user/Z%20appendix/Pasted%20image%2020221016181643.png)
> - 同理也可用上边的open close函数，判断数据集存不存在

## 9. 自己研究的宏

> [!自动获取路径]
> ![../../Z appendix/Pasted image 20221016225813.png](/img/user/Z%20appendix/Pasted%20image%2020221016225813.png)

> [!计算小数位数]
> - 对于ADSL类数据集中，计算其中所有数值变量的最大小数位数。（如WEIGHT,HEIGHT,AGE等变量）
> - 对于BDS类数据集中，paramcd存放不同检查项，aval存放对应检查项的数值，计算每个paramcd检查项对应的最大小数位数
> - ![../../Z appendix/Pasted image 20221016225932.png](/img/user/Z%20appendix/Pasted%20image%2020221016225932.png)

