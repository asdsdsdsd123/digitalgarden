---
{"dg-publish":true,"dg-home":null,"permalink":"/03 STAT/项目中的/生存分析/03 生存分析-分层（不分层）Cox模型求风险比HR及其CI/","dgPassFrontmatter":true}
---


## 1. 输出rtf 及编程注意事项

![../../../Z appendix/Pasted image 20221209223943 1.png](/img/user/Z%20appendix/Pasted%20image%2020221209223943%201.png)

## 2. sas代码

#统计分析/生存分析/生存差异的95CI

```sas
ods graphics;
/*风险比率HR (95% CI) */

/*分层的*/
ods select none;
ods output parameterestimates=hr_stra(keep=hazardratio hrlowercl hruppercl);
proc phreg data=adttee;
	model aval*cnsr(1)=trtpnn/ties=exact rl=wald/*rl=pl 改用不同的95%CI*/ alpha=.05;
 	strata &strata. ; /**指定宏，可选不同的分层因素：如敏感分析2/
run;
ods output close;

/*不分层的*/
ods output parameterestimates=hr_nostra(keep=hazardratio hrlowercl hruppercl);
proc phreg data=adttee;
	model aval*cnsr(1)=trtpnn/ties=exact rl=wald  alpha=.05;
run;
ods select all;

data adttee2_hr;
	length col1-col2 $200;
	set hr_stra(in=a) hr_nostra(in=b);
	if a then do;part=3;col1="分层风险比率 (95% CI)@{super [b]}";end;
		else if b then do;part=5;col1="未分层风险比率 (95% CI)@{super [d]}";end;
	 if hazardratio=. then col2='NE';
	 	else if hazardratio>. then do;
		 	if hrlowercl> . and hruppercl > . then 
		 		col2=trim(left(put(hazardratio, 8.2)))||' ('||trim(left(put(hrlowercl,8.2)))||', '||trim(left(put(hruppercl,8.2)))||')';
			if hrlowercl= . and hruppercl > . then 
		 		col2=trim(left(put(hazardratio, 8.2)))||' (NE, '||trim(left(put(hruppercl,8.2)))||')';
			if hrlowercl> . and hruppercl = . then 
		 		col2=trim(left(put(hazardratio, 8.2)))||' ('||trim(left(put(hrlowercl,8.2)))||', NE)';
			if hrlowercl= . and hruppercl = . then 
		 		col2=trim(left(put(hazardratio, 8.2)))||'(NE, NE)';
     end;
run;

```

## 3. 核心语句

> [!note]+ 核心语句 
> - `proc phreg` data=xx；
> 	- `model aval*cnsr(1)=trtpnn/ties=exact rl=wald/*rl=pl 改用不同的95%CI*/ alpha=.05;`
> 
> 	- *分层的：*
> 		- `strata &strata. ;`
> 	- *不分层的：*
> 		-`/*不写strata语句即可*/`
> 	- ods output `parameterestimates`=logrkp_stra `/*输出HR（风险比）及其95% CI到数据集*/`

