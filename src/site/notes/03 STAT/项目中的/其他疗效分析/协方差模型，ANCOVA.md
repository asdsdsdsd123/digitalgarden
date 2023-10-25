---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/项目中的/其他疗效分析/协方差模型，ANCOVA/","dgPassFrontmatter":true}
---


#统计分析/协方差

- ![../../../Z appendix/Pasted image 20221209224804.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020221209224804.png)

```ad-note
collapse: open
title: 例如以下sas代码，`也需要注意方向`
proc mixed data = raw;

   class trtpn;

   model pchg=trtpn `base`;

   `lsmeans trtpn/ diff cl`

run;
```

- [ ] 补充金赛的例子