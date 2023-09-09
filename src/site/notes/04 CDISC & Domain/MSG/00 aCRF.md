---
{"dg-publish":true,"permalink":"/04 CDISC & Domain/MSG/00 aCRF/"}
---


## 1. General Rule

### 1.1. 字体颜色，字体大小，填充颜色

> MSG 2.0这里有更新
> - 方框位置，一般放于左上角，平放一行，多的可再在下边放一行
> 	- domain: `DM (Demographic)`
> - 对于指定的变量信息/注释说明，需用`虚线框 `
> 	- ![](/img/user/Z appendix/Pasted image 20230816174324.png)
> - 字体字号颜色，`12号`，`arial斜体加粗`。`不同domian`间用`不同底色区分（BLUE-191 255 255, YELLOW-255 255 150, GREEN-150 255 150,  ORANGE-255 190 155）`，变量与域之间字体颜色`均为黑色，变量非粗体，域粗体。二者都不用斜体`（选中，Ctrl+E设置属性）
> - 必要时可用方框与箭头


-----
以下为MSG 1.0版本的内容：
-   方框位置，一般放于左上角，平放一行，多的可再在下边放一行
-   字体字号颜色，`12号`，`arial斜体加粗`。`不同domian`间用`不同底色区分（蓝 绿 黄 粉）`，变量与域之间字体颜色分别为红色与黑色（选中，Ctrl+E设置属性）
-   必要时可用方框与箭头

> [!要点]-
> - `domain--黑色字体`
> 	- ![](/img/user/Z appendix/Pasted image 20221018231517.png)
> - `Variables--红色字体`
> 	- ![](/img/user/Z appendix/Pasted image 20221018231548.png)
> - `可加注释之类(4)` 
> 	- ![](/img/user/Z appendix/Pasted image 20221018231817.png)
> - `co注释`
> 	- 能确定是哪个doman的co注释：
> 		- ![](/img/user/Z appendix/Pasted image 20221018232111.png)  ![](/img/user/Z appendix/Pasted image 20221018233914.png)
> 	- 不确定哪个domain的co注释：
> 		- ![](/img/user/Z appendix/Pasted image 20221018232132.png)
> - 如未发生伴随用药之类，`可不提交`
> 	- ![](/img/user/Z appendix/Pasted image 20221018232333.png)
> - relrec domain
> 	- ![](/img/user/Z appendix/Pasted image 20221018232545.png)  ![](/img/user/Z appendix/Pasted image 20221018232558.png)
> 
>

### 1.2. 删除边框文本及文件中自带的作者信息

> [!删除自带作者信息，一般英文项目需要]
> ![](/img/user/Z appendix/Pasted image 20221018230910.png)  ![](/img/user/Z appendix/Pasted image 20221018230920.png)


### 1.3. 超链接

- 必要时可加入超链接，要求被链接的地方和之前注释内容一样，可以只test不同 `导入PDF注释文件，之前的超链接信息会缺失，这里需要检查`


> [!加入超链接]
> ![](/img/user/Z appendix/Pasted image 20221018232836.png)   ![](/img/user/Z appendix/Pasted image 20221018232847.png)  
>   
> ![](/img/user/Z appendix/Pasted image 20221018232900.png)

最新MSG2.0 建议不用超链接
> For example, if a CRF page has 1 or more collected data points than another similar page, it is recommended that `both/all CRF pages be annotated`. In this instance, annotating just the new collection point and referencing the `annotations on another page (e.g., "See Page <n> for Annotations")  makes reviewability more difficult`

### 1.4. MSG 2.0中要求的递交书签


![acrf书签结构.excalidraw](acrf书签结构.excalidraw.md|2000)

- [ ] 结构这样需要研究下怎么方便生成

## 2. 项目中发现的要注意的点

### 2.1. --STAT与--REASND

> [!note] --STAT与--REASND
> 因为P21会检查，如果--STAT不为空，会检查--REASND是不是也不为空，因此若acrf能体现--REASND（未必明确指出是收集原因），也做到--REASND里
> - 一般orres中有这种信息的，给xxSTAT同时，给xxREASND
> - ![](/img/user/Z appendix/Pasted image 20221127114949.png)

### 2.2. Other orres/unit

> [!note] Other orres/unit
> IG的例子这么给的，检查是会按照IG的例子进行p21
> - `Other orres`
> 	- `orres=specify 内容`
> 	- `stresc=OTHER`
> 		- ![](/img/user/Z appendix/Pasted image 20221127115342.png)
> - `Other unit`
> 	- `orresu=Other`
> 	- `stresu不标`
> 	- `sepcify orresu in SUPPxx`
> 		- ![](/img/user/Z appendix/Pasted image 20221127115538.png)

### 2.3. 主题变量不能是Other

> [!note] 主题变量不能是Other，如`mhterm/aeterm/cmtrt等，value不能为Other/其他`
> - 主题变量值，如`mhterm/aeterm/cmtrt等，其value不能为OTHER`
> - ![](/img/user/Z appendix/Pasted image 20221127115659.png)
> - 注：像有的`发现类收集了OTHER TEST`，这个是数据收集问题，`P21解释`下就好。`或者可令PETEST=OTHER PE/PETESTCD=OTH`

### 2.4. 3.4版本与3.3版本参考

一般用3.3版本为基础

- 3.3没有的内容，3.4可相对补充，
> [!note] 某些在3.4的变量，而3.3没有
> - ![](/img/user/Z appendix/Pasted image 20221127121327.png)

- 3.4与3.3矛盾的地方以3.3为准，如--CLSIG 3.3是SUPP变量而--CLSIG是模型变量

### 2.5. CMINDC CMDISREA

CMINDC/CMDISREA选择多个

- CMINDC是模型变量，需遵循IG：CMINDC=MULTIPLE，其余值放CMINDC1-CMINDCn in SUPP。

> [!note]+ Multiple Values for a Non-Result Qualifier Variable
> - ![](/img/user/Z appendix/Pasted image 20230909103331.png)
> - ---
> - ![](/img/user/Z appendix/Pasted image 20230909103413.png)

- CMDISREA是SUPP变量，可以都放SUPP

## 3. 其他domain

```ad-note
collapse: open
title: 

- [AE](AE#1.1%20acrf)   
- [CM](CM#1.1%20acrf)    
- [CO](../SDTM/CO.md)    
- [DM](DM#1.1%20acrf)    
- [DS](DS#1.1%20acrf)  
- [DV](../SDTM/DV.md)
- [EG](EG#1.1%20acrf)    
- [FA](../SDTM/FA.md)
- [IE&TI](../SDTM/IE&TI.md)  
- [LB](LB#1.1%20acrf)     
- [RELREC](../SDTM/RELREC.md)      
- [TS](../SDTM/TS.md)
- [VS](VS#1.1%20acrf)    
- [SUPP](../SDTM/SUPP.md)

```

> 创建新的自定义域：一般以X/Y/Z开头，第二位为任意字母（体现大概域内容）或数字（一般不用）

