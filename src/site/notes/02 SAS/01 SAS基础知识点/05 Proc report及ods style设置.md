---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/05 Proc report及ods style设置/","dgPassFrontmatter":true}
---


## 1. report rtf相关的option设置

> [!report rtf相关的option设置]
> - options 
> 	- papersize=letter ps=max ls=max 
> 	- nofmterr nodate nonumber nobyline 
> 	- `formchar="|_---|+|---+=|-/\<>**"` 
> 	- `missing="  "`  `/*默认数值缺失显示为.，指定选项后，所有缺失值显示空格*/`
> 	- `orientation=landscape`;
> 	- ---
> 	- 其他选项解释，见下图：
> 		- ![../../Z appendix/Pasted image 20221015153639.png](/img/user/Z%20appendix/Pasted%20image%2020221015153639.png)

## 2. ods 语句

### 2.1. ods escapechar

- 用escapechar设定逃逸字符，`也可不设置直接用：(*ESC*)`--> 即是默认的逃逸字符
	- ods escapechar="`@`"
		- `@{newline}:换行`---`@n也是换行`
		- `@`{pageof}: 1/4
		- `@`{unicode 2265}:≥  --->`(*ESC*)`{unicode 2265 }。查找对应unicode码会有对应的特殊符号
		- `@`{supper 2}: <sup>2</sup>
		- `@`{sub 2}:<sub>2</sub>

> [!示例用法]
> - 除了放title类似文本中，也放到数值中，output rtf时，会自动escapechar
> 	- ![../../Z appendix/Pasted image 20221015183843.png](/img/user/Z%20appendix/Pasted%20image%2020221015183843.png)  
> 	- ![../../Z appendix/Pasted image 20221015184313.png](/img/user/Z%20appendix/Pasted%20image%2020221015184313.png)
> 	- ![../../Z appendix/Pasted image 20221126230147.png](/img/user/Z%20appendix/Pasted%20image%2020221126230147.png)

### 2.2. ods select all/none

- 结果输出、不输出html/listing 

> [!select all/none]
> ![../../Z appendix/Pasted image 20221015223109.png](/img/user/Z%20appendix/Pasted%20image%2020221015223109.png) 

### 2.3.  ods trace on/off+ods output

- (1) 先 ods trace on/off，可根据统计过程步，给出可选择的统计量关键字
	- ods trace `on/off`; 
	- 
- (2) 再在 ods output中，指定统计量关键字，输出统计量到指定数据集 #注意事项/输出统计量
	- ods output  `log-dataset-name`=`user-defined-dataset-name`;

> [!ods trace on/off及ods output示例如图]
> ![../../Z appendix/Pasted image 20221015224107.png](/img/user/Z%20appendix/Pasted%20image%2020221015224107.png)   ![../../Z appendix/Pasted image 20221015224201.png](/img/user/Z%20appendix/Pasted%20image%2020221015224201.png)

- (3) 也可通过统计的过程步中的output语句，输出统计量到数据集 #注意事项/输出统计量 

> [!通过统计的过程步中的output语句，输出统计量]
> - proc reg corr data = dehyd ;
> 	- model y = dose/ ss1 ss2 vif collinoint; 
> 		- `其中等号右边的变量是explanatory variable，涉及的explanatory越多，可能r2（%多少可以用模型解释）越大拟合越好，或可能趋近于非线性模型`
> 	- `output` `out`=outstat `p`=Predicted  `r`=Residual `stdr`=se_resid `student`=RStudent `h`=Leverage `cookd`=CooksD;
> 		-  outstat 为输出的数据集名称 ，指定p r stdr student h cookd为拟合诊断的统计量keyworld ，等号右边为变量名称
> 

> [!输出几何统计量]
> 
> -  #注意事项/输出几何统计量
> - ![../../Z appendix/Pasted image 20221016222908.png](/img/user/Z%20appendix/Pasted%20image%2020221016222908.png)  
> - ![../../Z appendix/Pasted image 20221016222918.png](/img/user/Z%20appendix/Pasted%20image%2020221016222918.png) 
> 
> 
>> [!sas代码]
>> - data pc;......
>> 	- `if aval>0 then geoaval=log(aval);`
>> 
>> - proc means data=pc noprint;
>> 	- output out=m(drop=`_:`)	
>> 	- `mean(geoaval)=geomean1 n(blq)=blqn std(geoaval)=geostd1` .....;
>> 
>> - data mean_;
>> 	- `geomean=exp(geomean1);`
>> 	- `geocv=sqrt(exp(geostd1**2)-1);`
>> 	- `geostd=exp(geostd1);`
>> 	---
>> 	- geomeanc=strip(putn(round(`geomean`,dot),strip(put(min(0.1+deci,10.4),??best.))));
>> 	- geocvc=strip(put(round(`geocv*100,0.1`),20.1));
>

#注意事项/输出几何统计量 几何均值及算数均值转化证明

- 首先，我们假设有n个正数$x_1, x_2, ..., x_n$，算术均值为：
$$\frac{1}{n}\sum_{i=1}^{n}x_i$$

- 几何均值为：xi乘到xn开根号n
$$\sqrt[n]{\prod_{i=1}^{n}x_i}$$
- 转换关系
	- 首先，几何均值取自然对数底的log值：
		- $$\ln\left(\sqrt[n]{\prod_{i=1}^{n}x_i}\right)=\frac{1}{n}\sum_{i=1}^{n}\ln(x_i)$$
		- 因为`对数函数和指数函数是互逆的`，即$\ln(e^x)=x$和$e^{\ln(x)}=x$。
		- 因此，我们可以将几何均值取自然对数底的log值与算术均值的对数相等。
	- 接下来，我们取两边的指数：
		- $$\sqrt[n]{\prod_{i=1}^{n}x_i}=e^{\frac{1}{n}\sum_{i=1}^{n}\ln(x_i)}$$
	- 然后，我们再对等式两边取指数平均值：
		- $$\text{exp}\left(\frac{1}{n}\sum_{i=1}^{n}\ln(x_i)\right)=\text{exp}\left(\ln\left(\sqrt[n]{\prod_{i=1}^{n}x_i}\right)\right)=\sqrt[n]{\prod_{i=1}^{n}x_i}$$

- 因此，我们证明了几何均值与算术均值的转化关系，即：[[../../Z appendix/Pasted image 20221016222918.png|结果同前图几何均值项下第一点]] 
$$\text{exp}\left(\frac{1}{n}\sum_{i=1}^{n}\ln(x_i)\right)=\sqrt[n]{\prod_{i=1}^{n}x_i}$$
其中，$\text{exp}$表示指数函数，$\prod$表示累乘符号。

### 2.4. ods rtf

- ods rtf file='e:\\1.rtf' style=rtftable `bodytitle`;
	- bodytitle, 默认情况下title与footnote在页眉页脚上，`bodytitle加入后出现在表头表尾位置`

> [!示例如图]
> ![../../Z appendix/Pasted image 20221015224923.png](/img/user/Z%20appendix/Pasted%20image%2020221015224923.png)

 [有些RTF语言可用，实现居中，缩进，加下划线等。](https://mp.weixin.qq.com/s/0LdqbWPN4JJw0bWKQ4mbFg)

### 2.5. ods excel

> [!note] 示例如图，但少用这种方法 
> ![../../Z appendix/Pasted image 20221015225149.png](/img/user/Z%20appendix/Pasted%20image%2020221015225149.png)

> [!note] listing输出到excel
> - ![../../Z appendix/Pasted image 20221126232449.png](/img/user/Z%20appendix/Pasted%20image%2020221126232449.png)
> ---
> - 这样好用些：
> 	- ![../../Z appendix/Pasted image 20221126232525.png](/img/user/Z%20appendix/Pasted%20image%2020221126232525.png)

## 3. style及rtf语句设定格式

### 3.1. 指定标题等类型的style

> [!指定标题等类型的style]
> ![../../Z appendix/Pasted image 20221015184759.png](/img/user/Z%20appendix/Pasted%20image%2020221015184759.png)

> [!输出sas template 全部自定义的style]
> 
> - 输出template定义的style
> 	- ![../../Z appendix/Pasted image 20221016185410.png](/img/user/Z%20appendix/Pasted%20image%2020221016185410.png)

### 3.2. rtf语句设定格式

> [!rtf表格表头嵌套的写法]
> - `简单常用的`, var2-var4嵌套:
> 	- column var1 `("total of vars"  var2-var4)`;
> 	- 示例:
> 		- ![../../Z appendix/Pasted image 20221015173840.png](/img/user/Z%20appendix/Pasted%20image%2020221015173840.png)
> - `复杂的`, 如下图是个三级的嵌套, 如第一个是性别嵌套col1与col2，第二层嵌套是自身的嵌套, 第三层可定义col1 col2 label, 虽然示例没有定义
> 	- column demo group order ("性别\brdrb\brdrs\brdrw10"   `(`  ("男性|N=10"  col1)      ("女性|N=9"  col2)    `)`)      col3;
> 		- ![../../Z appendix/Pasted image 20221015174115.png](/img/user/Z%20appendix/Pasted%20image%2020221015174115.png)

> [!涉及到的rtf语法]
> - ![../../Z appendix/Pasted image 20221015181028.png](/img/user/Z%20appendix/Pasted%20image%2020221015181028.png)
> 	- 上图`"\li220text"`  左缩进2字符=`"  text"`
> 	- 若`"\li240text"`，左缩进4字符=`"    text"`
> 			- 如图，输出样式不同
> 				- ![../../Z appendix/Pasted image 20221017230320.png](/img/user/Z%20appendix/Pasted%20image%2020221017230320.png)

- ---
补充：共有变量label换行：
 `column paramn col1 col2  ("所有受试者\par\ N = &pop.\brdrb\brdrs" col3-col7) ;`

![../../Z appendix/Pasted image 20230420170952.png](/img/user/Z%20appendix/Pasted%20image%2020230420170952.png)
	


## 4. report rtf subtitle

- ods `escapechar="^"`;
- title2 j=l  `"^S={font=('Times roman',10pt) color=red}#byval1"`
	- 其中`^S`设置了新title2的`style`，见其他章节
	- `#byval1`，title按照下面语句，by statement的第一个变量的值放到title2

> [!report rtf title随数据变化]
> - ![../../Z appendix/Pasted image 20221015155153.png](/img/user/Z%20appendix/Pasted%20image%2020221015155153.png)
> - ***注意：有时需要改动下位置：***
> 	- ![../../Z appendix/Pasted image 20230601105153.png](/img/user/Z%20appendix/Pasted%20image%2020230601105153.png)
> - ---
> - ![../../Z appendix/Pasted image 20221015155210.png](/img/user/Z%20appendix/Pasted%20image%2020221015155210.png)
> - 同时指定加粗显示: 
> 	- title2 j=c `"@S={font=('Times roman',10pt) font_weight=bold}Table 14.3.5.&titlen. Summary of &cat. Results - #byval2 (APaT)"`
> 	- 在公司toc里title2设置更方便:
> 		- ![../../Z appendix/Pasted image 20221015180119.png](/img/user/Z%20appendix/Pasted%20image%2020221015180119.png)

## 5. proc report

> [!variable usage-order]
> 
>> [!指定排序规则]
>> - order=option，指定排序规则
>> 	- ![../../Z appendix/Pasted image 20221015161932.png](/img/user/Z%20appendix/Pasted%20image%2020221015161932.png)
>
>> [!order选项， 指定order后的值只出现一次]
>> - ![../../Z appendix/Pasted image 20221015161804.png](/img/user/Z%20appendix/Pasted%20image%2020221015161804.png) 
>
>> [!显示missing order记录]
>> - `默认order为空的不显示这个order组`，需要加选项，proc report xxx `missing`
>> 	- 或者 define  sex/order `missng`; 
>> 	- `可在不同地方定义属性，但小范围的属性会覆盖大范围的属性`（如在define定义列宽会覆盖proc report中，覆盖style中） #注意事项/属性范围
>> 	- ![../../Z appendix/Pasted image 20221015164346.png](/img/user/Z%20appendix/Pasted%20image%2020221015164346.png)
> 
>> [!column指定order变量的顺序]
>> - 如column sex age name，age在sex后面，类似by/first.grp这种。如果column age sex；就没有first sex展示一次结果了
>> 	-  proc report; column中变量出现顺序，也影响最终RTF顺序（即使final数据集没问题）
> 

> [!compute语句块]
>
>> [!在group后插入空行, compute after]
>> - ![../../Z appendix/Pasted image 20221015170017.png](/img/user/Z%20appendix/Pasted%20image%2020221015170017.png)
>
>> [!可在group后(指定位置)放置txt文本]
>> - `注意这里用@n进行了换行，用proc report的split指定字符不行`
>> - ![../../Z appendix/Pasted image 20221015170044.png](/img/user/Z%20appendix/Pasted%20image%2020221015170044.png)
>> - ![../../Z appendix/Pasted image 20221015170148.png](/img/user/Z%20appendix/Pasted%20image%2020221015170148.png)
> 
>> [!定义compute line属性]
>> - 定义输出文本的属性
>> 
>> ```sas
>> compute before / style={just=l protectspecialchars=off};
>> 	text1="^{style[font_size=14pt color=blue background = yellow];
>> 		fontsize should be 14pt background color is yellow}";
>> 	line text1 $;
>>  endcomp;
>> ```
>> 
>> - 设定加多少宽度的空行
>> 
>> ```sas
>> compute after paramn/style={height=15};
>> 	line " ";
>> endcomp;	
>> ```
>

> [!break after分页]
> - order指定变量，该变量相同值只出现一次-->define sex/order
> 	- sex改变值, 产生新一页-->break after sex/page
> 	- `注意:分页变量必须先制定order usage`
> 	- ![../../Z appendix/Pasted image 20221015172312.png](/img/user/Z%20appendix/Pasted%20image%2020221015172312.png)
> - 可在proc report后加by语句, 先按照by语句分页,再按照break after page分页 

> [!变量太多，可分为两页展示，每页关键变量（ID）每页保留]
> - 简单示例:
> 	- ![../../Z appendix/Pasted image 20221015172658.png](/img/user/Z%20appendix/Pasted%20image%2020221015172658.png)
> - 项目中的: 
> 	- ![../../Z appendix/Pasted image 20221015172715.png](/img/user/Z%20appendix/Pasted%20image%2020221015172715.png)
> 

> [!定义proc report中的style]
> - define coln/display style(column)=[cellwidth=30% just=c]  style(header)=[just=c];
> 	- 定义该列的宽度，居中对齐。该列的标题header的对其方式，居中
> 	- 同前可在proc report中统一定义表格所有style(header)。在不同地方定义属性，小范围的权限更高。 #注意事项/属性范围 



