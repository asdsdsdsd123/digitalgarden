---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/07 proc sql/","dgPassFrontmatter":true}
---


## 1. select 语句块

> [!distinct]
> - 含有`distinct的部分写在select最前面`
> 	- 如：`distinct` name,age,sex。
> - distinct，`对distinct后边的所有变量都相同才去重`，而非只有distinct挨着的一个变量相同而去重
> ![../../Z appendix/Pasted image 20221016183129.png](/img/user/Z%20appendix/Pasted%20image%2020221016183129.png)

proc sql; select trt01an,count(distinct usubjid) into :pop1-  from xxx group by trt01an;
	会报错，sql创建多个宏变量不加group。如：[[02 SAS/01 SAS基础知识点/06 macro#1.2 创建宏变量\|06 macro#1.2 创建宏变量]]
	
## 2. order语句块

> [!order, 根据select后没有的变量排序]
> - 可根据select后没有的变量排序
> - ![../../Z appendix/Pasted image 20221016183328.png](/img/user/Z%20appendix/Pasted%20image%2020221016183328.png)

## 3. sql语句块中的if选择

> [!sql中的选择语句块]
> ![../../Z appendix/Pasted image 20221016183452.png](/img/user/Z%20appendix/Pasted%20image%2020221016183452.png)

