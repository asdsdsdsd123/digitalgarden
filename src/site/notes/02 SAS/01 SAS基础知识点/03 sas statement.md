---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/03 sas statement/","dgPassFrontmatter":true}
---


## 1. select选择

>[!note] select
>
>![../../Z appendix/Pasted image 20221013102743.png](/img/user/Z%20appendix/Pasted%20image%2020221013102743.png)  ![../../Z appendix/Pasted image 20221017231621.png](/img/user/Z%20appendix/Pasted%20image%2020221017231621.png)

## 2. output

> [!note] output后原来的值也会retain下来
> 
> ![../../Z appendix/Pasted image 20221013104934.png](/img/user/Z%20appendix/Pasted%20image%2020221013104934.png)

## 3. put，输出value到log

- [[02 SAS/01 SAS基础知识点/00 input & output data#5 SAS数据集导出外部文件\|00 input & output data#5 SAS数据集导出外部文件]]

> [!note]  put到log
> - 日志中自行`Put warning`，方便检查：
> 	- data步put语句，`put "WARNING: 自定义信息"`
> 		- 注意是固定写法，Warning必须全大写，再冒号，再空格
> 	- 宏里面，%if xxx %then `%put WARNING: 自定义信息`
> 
> - 其他put data变量值到Log:
> ```sas
> data null; 
>    a="haha"; 
>     b="meiyou";
>     put a b; 
>     put a= b=;
>     put "there is a question " a  
>         "there is a question " b ;
>     put "there is a question " a  ;
>     put "there is a question " b  ;
>     /*一个Put表示log的一行，即使分行写的*/
run;
> ```
> ```log
> *log*;
> haha meiyou
> a=haha b=meiyou
> there is a question haha there is a question meiyou
> there is a question haha
> there is a question meiyou
> ```
> - [[02 SAS/01 SAS基础知识点/00 input & output data#5 SAS数据集导出外部文件\|00 input & output data#5 SAS数据集导出外部文件]]
> 

## 4. retain/by-first,last.variable

>[!note] retain/by-first,last.variable
> - `retain sum 0`; sum=sum+var; <--> sum+var;
> 	- 0为给sum的初始值，每次sum的值在观测迭代中不初始化，该值保留
> - `by var;` 后可使用`first.var/last.variable`
> 	- ![../../Z appendix/Pasted image 20221013225112.png](/img/user/Z%20appendix/Pasted%20image%2020221013225112.png)

## 5. 与do循环语句一起用的语句

> [!note] 与do循环语句一起用的语句,控制循环进程
>> [!note] leave
>> ![../../Z appendix/Pasted image 20221013225823.png](/img/user/Z%20appendix/Pasted%20image%2020221013225823.png)
> 
>> [!note] continue
>> ![../../Z appendix/Pasted image 20221013225925.png](/img/user/Z%20appendix/Pasted%20image%2020221013225925.png)
>
>> [!note] goto
>> ![../../Z appendix/Pasted image 20221013230021.png](/img/user/Z%20appendix/Pasted%20image%2020221013230021.png)
>

## 6. call系列语句

- `call missing(var1,var2,var3)`，
	- 将括号内三个变量都放空（初始化，置为missing）。`原来没有的变量，从未设置的，默认为数值型`
- `call execute("sas program code")`，运行括号内的SAS代码 

## 7. array数组

> [!note] array
> - new(✳)，✳是数组的维度。
>> [!note] 常用：将数据集中的变量放数组。新建一堆变量，放数组
>>  ![../../Z appendix/Pasted image 20221013231724.png](/img/user/Z%20appendix/Pasted%20image%2020221013231724.png)
> 
>> [!note] 创建临时变量，Array num(2) \_temporary_ \(2 9)
>> - 其中不可写为….. num(\*)….，因只存放临时变量，此时需要写清楚具体变量个数
>> - 如下图示例中，没有把值加到变量中，也是创建的临时的数组。省了\temporary
>> 	- ![../../Z appendix/Pasted image 20221013232529.png](/img/user/Z%20appendix/Pasted%20image%2020221013232529.png)
>> - 写成\*报错
>> 	- ![../../Z appendix/Pasted image 20221013232644.png](/img/user/Z%20appendix/Pasted%20image%2020221013232644.png)

## 8. attrib分配属性

> [!attrib]
> ![../../Z appendix/Pasted image 20221015112119.png](/img/user/Z%20appendix/Pasted%20image%2020221015112119.png)