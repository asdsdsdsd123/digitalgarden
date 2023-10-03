---
{"dg-publish":true,"dg-home":null,"permalink":"/02 SAS/01 SAS基础知识点/02 sas numeric function/","dgPassFrontmatter":true}
---


- 关于有缺失值的数值运算，使用函数与直接算数计算，当某一参数缺失时，所得结果不同，
	- 如`y1=a+b+c, y2=sum(a,b,c)`，其中若c为缺失，则y1为缺失，y2为a+b的值
	- 如`y1=(a+b+c)/3, y2=mean(a,b,c)`，若c缺失，则y1为缺失，y2为(a+b)/2的值

## 1. 数值运算函数

> [!note] 数值运算函数
> 
> - nmiss(var1,var2) 返回变量中缺失的个数，要求var1/2数值型
> - n(var1,var2)返回变量中非缺失的个数，要求var1/2数值型
> - cmiss(var1,var2)返回变量中缺失的个数，`不要求var1/2数值型`
> - floor, int, ceil和round都是对带小数的数值操作
> 	- floor是向下取整，int是直接抹平小数部分，ceil是向上取整，round是四舍五入
> - mod(10,4)求10/4的取余数的结果
> - 取x的自然对数，以e为底数，log(x)
> 	- 取x的10为底的对数，log10(x)-->log10(100)=2
> 	- e的x次方， Exp(x)
> - sum/mean/std/max/min/median/(var1,var2)
> - abs(var1)取绝对值，sign(var1)返回-1 0 1，sqrt(var1)开根号
> - input(x, ??best.)，字符转数值
> 		- 不加??：x值为'56'转化为56，x值为'us'不能转化，日志报错
> 		- 加??：x值为'56'转化为56，x值为'us'转化为缺失，日志不报错
> - put(x，z3.)：若X值为56，则转化后值为“056”
> 		- put(x，3.)：若X值为56，则转化后值为“空格56”；
> - 逻辑表达式，adt-trtsdt+(adt >= trtsdt)
> 	- `adt>=trtsdt的值为1，否则为为0`

## 2. 日期函数

### 2.1. 定义日期的函数
> [!note] 定义日期的函数
> - mdy(mm,dd,yy)，返回指定时间距1960.1.1的天数
> 	- today()/date()，返回今天距1960.1.1的天数
> - datetime()，返回今天距1960.1.1午夜（0:0:0）的秒数
> - month/year/day/weekday(`yymmdd10.数值数据`)。
> 	- datepart/timepart(`is8601dt.数值数据`)
> - 已知date，在基础上添加时分秒信息，生成datetime型数据：dhms(date,12,0,0)或dhms("01jan2001"d,10,10,10)
> 	- 定义常量，
> 		- "01jan2001"d，
> 		- "01jan2001T10:00:00"dt或"2001-01-01T10:00:00"dt

### 2.2. 日期间隔类函数

> [!note] yrdif，返回两个日期间隔多少天（basis指定基准）
> - yrdif(sdate,edate,basis)，返回两个日期间隔多少个basis
> 	- basis取值，'30/360'，'ACT/360'，'ACT/ACT'
> 		- 30/360：意思为一个月按照30天计算/一年按照360天计算
> 		- ACT/360：意思为一个月按照实际情况计算/一年按照360天计算
> 		- ACT/ACT：意思为一个月按照实际情况计算/一年按照实际情况计算
> 	
> 	- ![../../Z appendix/Pasted image 20221013103955.png](/img/user/Z%20appendix/Pasted%20image%2020221013103955.png)
> 

> [!note] intck，返回两个日期间隔多少个年月日/星期（interval）
> - intck(interval, sdate, edate)
> 	- interval可为"month" "year" "week" "day"

>[!note]+ intnx，只知道起点，间隔，求终点日期。可用于日期填充
>- intnx(interval,'05feb94'd,`4`,'begining')。
>- 给定起点05feb1994，返回间隔`4` Interval的日期。
>		- 指定为`0`时，返回当年year/月month第一天最后一天（begining,end）
> - interval可为"month" "year" "week" "day"，指定了间隔
> - 第四个参数，默认为'beigining' 可为"middle" "end" "same"
>	
> ```sas
> intnx('month','05feb94'd,0,'end') ;/*返回当月的最后一天*/
> intnx('month','05feb94'd,0,'beginging') ;/*返回当月的第一天*/
> /*修改moth-->year，返回当年的第一天/最后一天。*/
> ```


## 3. 其他函数

### 3.1. ifc/ifn

> [!note] ifc/ifn
>
> - ifn返回值是数值型，ifc返回值是字符型
> - 规则类似，ifn，判断state是否为"PA"，若为"PA"则raten=7.25，若不为PA则新变量raten=5.25
> 
> ![../../Z appendix/Pasted image 20221013103138.png](/img/user/Z%20appendix/Pasted%20image%2020221013103138.png)

### 3.2. lag/dif函数

> [!note] lag/dif函数
> - lag(arg)，返回上一次lag函数执行时的arg值。
> - dif(arg)=当前arg值-lag(arg)值
> - ![../../Z appendix/Pasted image 20221013223303.png](/img/user/Z%20appendix/Pasted%20image%2020221013223303.png)
> - 注意事项：
> 	- `返回上一次lag函数执行时的arg（在前边有值即可，非必须前一个观测的）`
> 	- 由下可得，`最好用的时候不要写在if判断句内`
> 	- ![../../Z appendix/Pasted image 20221013223812.png](/img/user/Z%20appendix/Pasted%20image%2020221013223812.png)
> 	- 上图中，因为只用当X>2时才会调用LAG，所以第一次调用LAG时，X=3；当X=5时，第二次调用LAG，此时LAG(5)=3


