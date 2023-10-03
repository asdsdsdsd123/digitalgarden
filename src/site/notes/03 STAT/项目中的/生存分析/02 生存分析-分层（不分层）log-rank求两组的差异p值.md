---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/02 生存分析-分层（不分层）log-rank求两组的差异p值/","dgPassFrontmatter":true}
---


## 1. 输出rtf 及编程注意事项

![../../../Z appendix/Pasted image 20221209223943.png](/img/user/Z%20appendix/Pasted%20image%2020221209223943.png)

## 2. sas代码

#统计分析/生存分析/生存差异log_rank检验p值

```sas
ods graphics;
/*Log-Rank 检验P值*/
/*分层的*/
ods select none;
ods output trendtests=logrkp_stra;
proc lifetest data=adttee;
	time aval*cnsr(1);
	strata &strata./group=trtpn test=logrank trend;
run;  /**指定宏，可选不同的分层因素：如敏感分析2/

/*不分层的*/
ods output close;
ods output trendtests=logrkp_nostra;
proc lifetest data=adttee;
	time aval*cnsr(1);
	strata trtpn/test=logrank trend;
run;
ods select all;

data adttee2_p;
	length col1-col2 $200;
	set logrkp_stra(in=a) logrkp_nostra(in=b);
	if a then do;part=4;col1="分层双侧Log-Rank 检验P值@{super [c]}";end;
		else if b then do;part=6;col1="未分层双侧Log-Rank 检验P值@{super [e]}";end;
	if not missing(probz) then col2=strip(put(probz,pval.));
run;
```

## 3. 核心语句

> [!note]+ 核心语句 
> - proc lifetest data=xx；
> 	- `time aval*cnsr(1);/*和前面KM一样*/`
> 	- *分层的：*
> 		- `strata &strata./group=trtpn test=logrank trend;`
> 	- *不分层的：*
> 		- `strata trtpn/test=logrank trend;`
> 	- ods output `trendtests`=logrkp_stra `/*输出p值到数据集*/`





