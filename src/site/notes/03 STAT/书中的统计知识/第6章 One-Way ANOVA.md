---
{"dg-publish":true,"dg-home":null,"cssclasses":["code-wrap"],"permalink":"/03 STAT/书中的统计知识/第6章 One-Way ANOVA/","dgPassFrontmatter":true}
---


## 1. Introduction

比较`两组或两组以上`独立样本`均值是否相等`。两组的均值变异越大，则假设越不成立（两组均值相等）。

`统计量为F（即组间方差差异/组内方差差异）`。当零假设成立时，组间的变异应该与组内的变异相同，F应该接近1。Withan `F-distribution with k–1 upper degrees of freedom` and `N–k lower degrees of freedom.假设有K个组。` 

<mark style="background: #FFB8EBA6;">要求样本正态分布，等方差。</mark>

适用于`分析多剂量组间的差异`，或基于患者背景信息(如种族、年龄组或疾病严重程度)的不同strata（分层）之间的效应

--------------------------------参考之前别的资料---------------------------------------
方差分析用于两个/两个以上样本均值的比较，`还可分析两个或多个研究因素的交互作用，如多因素方差分析（回归方程的线性假设检验等）`，是对总变异进行分解（如总变异分解为组间变异/处理变异，组内变异-/误差变异），对分解部分进行比较，判断不同样本的总体均数是否相等（如果各个组的总体均数相等，没有差异，则不区分组间，组内。则平均组间变异（平均XX变异--XX均方），平均组内变异都是差不多一样的，二者比值是1。分析组间的变异性相对于组内的变异性，以确定组间的差异是否有意义或显著性。--组内的变异因为误差SS（Error））

以下例子展示组间、组内变异是如何算出来的。
总变异：SS总，总均方：MS总。

![../../Z appendix/Pasted image 20230130221606.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130221606.png)

## 2. Synopsis

![../../Z appendix/Pasted image 20230130221626.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130221626.png)

## 3. Examples
开发血清素摄取抑制药，治疗焦虑症。设置了`三个剂量组，25 mg SN-X95, 100 mg SN-X95, or placebo` ，通过量表评分，效应变量为多项评分（0-5）的总和。比较不同剂量组的评分差异，有无统计学意义

数据及相关统计量：

![../../Z appendix/Pasted image 20230130221825.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130221825.png)

![../../Z appendix/Pasted image 20230130221849.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130221849.png)

![../../Z appendix/Pasted image 20230130221918.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130221918.png)

### 3.1. sas代码

proc glm data = gad plots = boxplot;
	class dosegrp;
	model hama = dosegrp;
	means dosegrp / `hovtest t dunnett`(`‘PB’`); 
	`contrast ‘ACTIVE vs. PLACEBO’ dosegrp 0.5 0.5 –1`; 
run;

![../../Z appendix/Pasted image 20230130222120.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130222120.png)  

该方法统计量F，P值：
![../../Z appendix/Pasted image 20230130222244.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130222244.png)

- ---

方差齐性检验：
![../../Z appendix/Pasted image 20230130222346.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130222346.png)

- ---

两两比较：
![../../Z appendix/Pasted image 20230130222507.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130222507.png)
，，，，，，，，，，，，，，，，，，
![../../Z appendix/Pasted image 20230130222528.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130222528.png)

- ---

指定组（如合并给药组与安慰剂组）对比：

![../../Z appendix/Pasted image 20230130223112.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130223112.png)

## 4. 注意事项

### 4.1. SAS函数计算F临界值，p值。

计算F临界值，QUANTILE(‘F’,1–α, udf, ldf) --> quantile(‘F’,0.95,2,45); 
计算p值，pval = 1-probf(6.97,2,45) 

### 4.2. ANOVA表，如下：

![../../Z appendix/Pasted image 20230130223335.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130223335.png)


### 4.3. one-way ANOVA前提：

`数据正态分布；各组方差齐性；样本独立`

（1）ods graphics，可采用SAS ods graphics板块（ods graphics on/off）绘图。
<mark style="background: #FFB8EBA6;">Proc glm data=xx plots=图形类型</mark>（如检查分布的常用的BOXPLOT DIAGNOSTICS ）

![../../Z appendix/Pasted image 20230130223746.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130223746.png)

（2）正态性：同时可通过PROC UNIVARIATE/GLM的一些选项，进行Shapiro-Wilk test or Kolmogorov-Smirnoff 正态检验。
若不满足正态性，可对其进行对数转化，或可采用Kruskal-Wallis 代替单因素方差分析。

（3）方差齐性：<mark style="background: #FFB8EBA6;">通过PROC GLM的means语句的HOVTEST 选项，进行方差齐性检验。</mark>
如果`方差不齐，可采用Welch’s test，比较组间均值，需要 add the WELCH option to the MEANS statement in PROC GLM `

### 4.4. 多组ANOVA，拒绝H0，后的两两比较
（1）所有组两两间比较，means dosegrp/… `t`…; 

与前面multiple ttest（第二章）一样，多次两两检验，会增大α。如果每个两两检验都在α=0.05的显著性水平下进行，所有的两两比较中，则至少有一个错误结论的机会。

（2）比较所有控制组与对照组，means dosegrp/…`dunnett`…;
`dunnett（‘PB’），PB是前边means变量dosegrp的值，指定control组`
indicate that each dose group is to be compared with the placebo group

Dunnett’s test controls the overall significance level 

### 4.5. contrast 'ACTIVE vs. PLACEBO' dosegrp 0.5 0.5 -1;相关解释

![../../Z appendix/Pasted image 20230130224039.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224039.png)

![../../Z appendix/Pasted image 20230130224056.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224056.png)

![../../Z appendix/Pasted image 20230130224117.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224117.png)

数值系数设置可随意，如下，LO HI两两比较：
![../../Z appendix/Pasted image 20230130224144.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224144.png)

### 4.6. 'no difference’means ‘insufficient evidence to detect a difference’. 

### 4.7. 单组的置信区间
（1）公式手动计算

![../../Z appendix/Pasted image 20230130224320.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224320.png)

（2）proc glm中`estimate语句计算 mean and standard error for every group`


![../../Z appendix/Pasted image 20230130224340.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224340.png)

![../../Z appendix/Pasted image 20230130224410.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224410.png)

`与proc means mean std ;by dosegrp;var hama;run;相同`

### 4.8. 两组比较的置信区间，

95% confidence intervals `obtained for the mean difference between any pair of groups`

（1）公式手动计算

![../../Z appendix/Pasted image 20230130224519.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224519.png)

（2）proc glm中`estimate语句，计算两组间差异的置信区间`

![../../Z appendix/Pasted image 20230130224534.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230130224534.png)

<mark style="background: #FFB8EBA6;">均值差异的置信区间不包含0，表示均值显著不同</mark>

### 4.9. 两组的方差分析

与前边的two sample t-test应用一样，也可以采用proc glm的方法，可在proc glm加上hovtest选项，检查方差齐性。
`若方差不齐，可用PROC GLM加WELCH 选项，与Satterthwaite t-test结果相同`

### 4.10. Type I, II, III, and IV sums of squares

以上四种平方和，在One way anova中，没有区别，SAS提供了Type I and III result

### 4.11. estimate, contrast语句(网上查到的)

可通过ESTIMATE/CONTRAST比较结果，计算该自定义的差异--[参考如下](https://www.cnblogs.com/SAS-T/p/15541777.html)

![../../Z appendix/Pasted image 20230219165841.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219165841.png)

![../../Z appendix/Pasted image 20230219165855.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219165855.png)


<span style="background:#fff88f">ESTIMATE</span>:
例如：（1）  

![../../Z appendix/Pasted image 20230219165926.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219165926.png)  

（2）  

![../../Z appendix/Pasted image 20230219165944.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219165944.png)

`CONTRAST:test安慰剂与治疗用药组没有差异：(0.5+0.5-1--->验证列出的组0)`

![../../Z appendix/Pasted image 20230219170101.png|undefined](/img/user/Z%20appendix/Pasted%20image%2020230219170101.png)

#### 4.11.1. estimate项目的补充

```sas
estimate '0.01% vs, Pbo Month 12' trt01pn 1 0 -1 
		trt01pn*avisitn 1 0   0 0   -1 0 / exp cl;
		
   	estimate '0.02% vs, Pbo Month 12' trt01pn 0 1 -1 
	   	trt01pn*avisitn  0 0  1 0   -1 0  / exp cl;
	   	
   	estimate '0.02% vs, 0.01 Month 12' trt01pn -1 1 0 
	   	trt01pn*avisitn  -1 0   1 0  0 0  / exp cl;
```

<style> .container {font-family: sans-serif; text-align: center;} .button-wrapper button {z-index: 1;height: 40px; width: 100px; margin: 10px;padding: 5px;} .excalidraw .App-menu_top .buttonList { display: flex;} .excalidraw-wrapper { height: 800px; margin: 50px; position: relative;} :root[dir="ltr"] .excalidraw .layer-ui__wrapper .zen-mode-transition.App-menu_bottom--transition-left {transform: none;} </style><script src="https://cdn.jsdelivr.net/npm/react@17/umd/react.production.min.js"></script><script src="https://cdn.jsdelivr.net/npm/react-dom@17/umd/react-dom.production.min.js"></script><script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@excalidraw/excalidraw@0/dist/excalidraw.production.min.js"></script><div id="交互作用的estimateexcalidraw.md1"></div><script>(function(){const InitialData={"type":"excalidraw","version":2,"source":"https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/1.9.19","elements":[{"type":"text","version":264,"versionNonce":1164291947,"isDeleted":false,"id":"tyFClUxk","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-366.7755732166059,"y":-103.99361716886673,"strokeColor":"#000000","backgroundColor":"transparent","width":545.85986328125,"height":75,"seed":898864187,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"fontSize":20,"fontFamily":1,"text":"trt01pn（1,2,3） * avisitn1（6M, 12M）：\n1（6M） 1（12M） 2（6M） 2（12M） 3（6M） 3（12M）\n0.01阿托品        0.02阿托品        安慰剂","rawText":"trt01pn（1,2,3） * avisitn1（6M, 12M）：\n1（6M） 1（12M） 2（6M） 2（12M） 3（6M） 3（12M）\n0.01阿托品        0.02阿托品        安慰剂","textAlign":"left","verticalAlign":"top","containerId":null,"originalText":"trt01pn（1,2,3） * avisitn1（6M, 12M）：\n1（6M） 1（12M） 2（6M） 2（12M） 3（6M） 3（12M）\n0.01阿托品        0.02阿托品        安慰剂","lineHeight":1.25,"baseline":67},{"type":"freedraw","version":65,"versionNonce":1889505477,"isDeleted":false,"id":"eNhFZC6cPc9i1y-Nl5pMv","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-364.40560861699646,"y":-28.817733537314325,"strokeColor":"#ed0707","backgroundColor":"transparent","width":73.90478515625,"height":1.1428571428571388,"seed":1904302283,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.38096400669638797,0],[5.333339146205333,-0.3809465680803612],[10.285714285714278,-1.1428571428571388],[16.380964006696388,-1.1428571428571388],[23.238106863839278,-1.1428571428571388],[33.52382114955361,-1.1428571428571388],[38.85714285714283,-1.1428571428571388],[43.04764229910717,-1.1428571428571388],[46.47621372767861,-1.1428571428571388],[48.76192801339283,-1.1428571428571388],[51.04764229910717,-1.1428571428571388],[53.71428571428572,-1.1428571428571388],[57.14285714285717,-1.1428571428571388],[60.57142857142861,-1.1428571428571388],[64,-1.1428571428571388],[67.42857142857139,-1.1428571428571388],[70.09524972098217,-1.1428571428571388],[73.52382114955361,-1.1428571428571388],[73.90478515625,-1.1428571428571388],[73.90478515625,-1.1428571428571388]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":60,"versionNonce":393382411,"isDeleted":false,"id":"B44n4G3s2PbULPhZwic_-","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-192.21510917503218,"y":-29.198680105394686,"strokeColor":"#ed0707","backgroundColor":"transparent","width":115.42857142857144,"height":0,"seed":1419627947,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[3.8095005580357224,0],[13.714285714285722,0],[23.619035993303555,0],[35.047607421875,0],[49.523786272321445,0],[63.23807198660717,0],[76.95235770089289,0],[87.61903599330356,0],[95.23807198660717,0],[101.33332170758928,0],[105.90475027901789,0],[110.85714285714289,0],[115.047607421875,0],[115.42857142857144,0],[115.42857142857144,0]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":61,"versionNonce":911945765,"isDeleted":false,"id":"6xgxjeYdQbUv4fwTlu1mY","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-1.7388954473536273,"y":-26.91296581968038,"strokeColor":"#ed0707","backgroundColor":"transparent","width":71.6190011160715,"height":0.761910574776806,"seed":337437061,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[2.666643415178612,0],[8.761858258928612,-0.761910574776806],[16.000000000000057,-0.761910574776806],[25.142857142857167,-0.761910574776806],[34.28571428571428,-0.761910574776806],[44.19042968750006,-0.761910574776806],[51.80950055803572,-0.761910574776806],[57.5237862723215,-0.761910574776806],[58.28571428571428,-0.761910574776806],[59.80950055803572,-0.761910574776806],[63.23807198660717,-0.761910574776806],[67.04757254464283,-0.761910574776806],[70.09521484375006,-0.761910574776806],[71.23807198660717,-0.761910574776806],[71.6190011160715,-0.761910574776806],[71.6190011160715,-0.761910574776806]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":59,"versionNonce":1414205611,"isDeleted":false,"id":"3VICZsnBNy-FXG8VGo6wb","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-376.977037188425,"y":-59.29391238776074,"strokeColor":"#ed0707","backgroundColor":"transparent","width":22.857142857142833,"height":1.9047677176339448,"seed":1753052133,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.7619105747767776,0],[2.2857142857142776,0],[5.714285714285722,-0.761910574776806],[9.142857142857167,-1.5238211495535836],[11.809535435267833,-1.9047677176339448],[14.095249720982167,-1.9047677176339448],[15.619053431919667,-1.9047677176339448],[17.523821149553555,-1.9047677176339448],[19.809535435267833,-1.9047677176339448],[20.952392578124943,-1.9047677176339448],[21.714285714285722,-1.9047677176339448],[22.476196289062443,-1.9047677176339448],[22.857142857142833,-1.9047677176339448],[22.857142857142833,-1.9047677176339448]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":54,"versionNonce":908845957,"isDeleted":false,"id":"_Hh7le02Qg1nv-Gt_EOxJ","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-288.2151091750322,"y":-58.91296581968038,"strokeColor":"#ed0707","backgroundColor":"transparent","width":12.952357700892861,"height":2.666660853794667,"seed":865042149,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.380929129464306,-0.3809465680803612],[2.285714285714306,-0.761910574776806],[4.571428571428584,-1.1428571428571672],[7.238071986607139,-1.5238037109375],[9.523786272321445,-2.285714285714306],[11.809500558035722,-2.666660853794667],[12.571428571428584,-2.666660853794667],[12.952357700892861,-2.666660853794667],[12.952357700892861,-2.666660853794667]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":55,"versionNonce":2107229003,"isDeleted":false,"id":"zwBFUPrLpk1wf_ykEYIY-","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-192.21510917503218,"y":-58.91296581968038,"strokeColor":"#ed0707","backgroundColor":"transparent","width":25.90475027901789,"height":0.3809465680803612,"seed":81459979,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.7618931361607224,-0.3809465680803612],[1.1428571428571672,-0.3809465680803612],[4.190464564732167,-0.3809465680803612],[8,-0.3809465680803612],[13.333321707589278,-0.3809465680803612],[18.285714285714278,-0.3809465680803612],[22.85714285714289,-0.3809465680803612],[24.761893136160722,-0.3809465680803612],[25.90475027901789,-0.3809465680803612],[25.90475027901789,-0.3809465680803612]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":53,"versionNonce":340732645,"isDeleted":false,"id":"X1ixc_ei4cS0wyVAp_XIM","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-102.3103588960143,"y":-58.53201925160005,"strokeColor":"#ed0707","backgroundColor":"transparent","width":20.571428571428555,"height":0,"seed":661911243,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[1.5238211495535552,0],[5.714285714285666,0],[9.523821149553555,0],[13.714285714285666,0],[17.14285714285711,0],[19.428571428571388,0],[20.571428571428555,0],[20.571428571428555,0]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":57,"versionNonce":2075730411,"isDeleted":false,"id":"RI5QwRuUqDUFAY3C1JyhW","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-2.1198943312821825,"y":-57.38916210874288,"strokeColor":"#ed0707","backgroundColor":"transparent","width":17.904785156250057,"height":0.7618931361607224,"seed":252270571,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.7619280133928896,0],[2.2857142857142776,0],[3.428571428571445,0],[5.714285714285722,0],[7.619070870535722,-0.7618931361607224],[9.142857142857167,-0.7618931361607224],[11.428571428571388,-0.7618931361607224],[14.095284598214278,-0.7618931361607224],[16.000000000000057,-0.7618931361607224],[17.523856026785722,-0.7618931361607224],[17.904785156250057,-0.7618931361607224],[17.904785156250057,-0.7618931361607224]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":73,"versionNonce":290620997,"isDeleted":false,"id":"tee0x1beXGGF0qJvL_Fs9","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":87.53347412286695,"y":-55.8597158551766,"strokeColor":"#ed0707","backgroundColor":"transparent","width":20.13324644270108,"height":0,"seed":1799519819,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.9151742241691636,0],[4.575703552554273,0],[8.236316665085155,0],[12.354474997627676,0],[14.642368665977642,0],[16.472633330170197,0],[17.845352774351113,0],[18.760526998520277,0],[19.675617438543668,0],[20.13324644270108,0],[20.13324644270108,0]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":61,"versionNonce":1202957451,"isDeleted":false,"id":"2ilyca9gXH7suCmqcWPuj","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-338.46718257735466,"y":-58.147588577490154,"strokeColor":"#070fed","backgroundColor":"transparent","width":27.454388883617014,"height":1.3727194441808592,"seed":1991618597,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.9151742241691636,-0.4575661660481387],[2.287893668349966,-0.4575661660481387],[4.5757454446271595,-1.3727194441808592],[6.863597220904239,-1.3727194441808592],[10.524210333435121,-1.3727194441808592],[14.642368665977642,-1.3727194441808592],[17.38780755433936,-1.3727194441808592],[19.675659330616497,-1.3727194441808592],[21.50596588688194,-1.3727194441808592],[22.878685331062798,-1.3727194441808592],[23.793817663159075,-1.3727194441808592],[24.708949995255352,-1.3727194441808592],[25.62412421942446,-1.3727194441808592],[26.99684366360532,-1.3727194441808592],[27.454388883617014,-1.3727194441808592],[27.454388883617014,-1.3727194441808592]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":54,"versionNonce":1275301,"isDeleted":false,"id":"8KjjT3SEHdbP2fCGBK1t4","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-250.61313814978013,"y":-57.690022411442015,"strokeColor":"#070fed","backgroundColor":"transparent","width":26.996843663605375,"height":0.4575661660481387,"seed":2091306629,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[1.3727194441808592,-0.4575661660481387],[10.524210333435121,-0.4575661660481387],[13.26964922179684,-0.4575661660481387],[17.845352774351056,-0.4575661660481387],[20.590791662712775,-0.4575661660481387],[23.793817663159075,-0.4575661660481387],[26.08166943943621,-0.4575661660481387],[26.996843663605375,-0.4575661660481387],[26.996843663605375,-0.4575661660481387]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":56,"versionNonce":1430512427,"isDeleted":false,"id":"G_vI3VeQFwjRwoW_-d9VE","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-150.8621639445896,"y":-58.60515474353829,"strokeColor":"#070fed","backgroundColor":"transparent","width":21.04833688272447,"height":0,"seed":1624340843,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.9151323320962774,0],[3.202984108373414,0],[5.490877776723437,0],[8.236316665085155,0],[11.439300773458513,0],[14.184739661820231,0],[16.93017855018195,0],[18.76048510644739,0],[20.133204550628193,0],[21.04833688272447,0],[21.04833688272447,0]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":58,"versionNonce":2076145925,"isDeleted":false,"id":"SfzTUcsm8CxKLFYMu3LDS","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-54.771802851929976,"y":-57.690022411442015,"strokeColor":"#070fed","backgroundColor":"transparent","width":28.827108327797987,"height":0.9151323320962774,"seed":41209605,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.45754522001175246,0],[3.660613112530882,0],[6.863597220904353,0],[10.06658132927771,-0.9151323320962774],[13.727194441808592,-0.9151323320962774],[19.21807221853203,-0.9151323320962774],[21.963511106893634,-0.9151323320962774],[24.251404775243714,-0.9151323320962774],[26.08166943943627,-0.9151323320962774],[27.45438888361707,-0.9151323320962774],[28.369563107786234,-0.9151323320962774],[28.827108327797987,-0.9151323320962774],[28.827108327797987,-0.9151323320962774]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":58,"versionNonce":1020544459,"isDeleted":false,"id":"1R_SHarE4Zrlq50FMhIMk","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":39.94583879654891,"y":-56.774869133309295,"strokeColor":"#070fed","backgroundColor":"transparent","width":29.284653547809626,"height":2.2878727223135797,"seed":1463816747,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.4575452200116388,-0.4575661660481387],[1.3727194441808024,-0.4575661660481387],[5.0333325567116844,-1.3727194441808592],[9.151490889254205,-2.2878727223135797],[11.896929777615924,-2.2878727223135797],[16.01508811015856,-2.2878727223135797],[19.218072218531915,-2.2878727223135797],[21.50596588688188,-2.2878727223135797],[24.2514047752436,-2.2878727223135797],[26.539214659447907,-2.2878727223135797],[28.36956310778612,-2.2878727223135797],[29.284653547809626,-2.2878727223135797],[29.284653547809626,-2.2878727223135797]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":57,"versionNonce":1574178917,"isDeleted":false,"id":"91CDiDCMpSP2VX3NMwHni","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":129.1726026683043,"y":-56.31728202122474,"strokeColor":"#070fed","backgroundColor":"transparent","width":32.487721440328755,"height":1.3727194441808592,"seed":408756645,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055764,"link":null,"locked":false,"points":[[0,0],[0.9151742241691636,0],[4.118158332542521,0],[8.693861885096794,0.9151532781327205],[13.26964922179684,1.3727194441808592],[16.01508811015856,1.3727194441808592],[21.505965886881995,1.3727194441808592],[25.624124219424516,1.3727194441808592],[28.827108327797873,1.3727194441808592],[30.657372991990428,1.3727194441808592],[31.57254721615959,1.3727194441808592],[32.487721440328755,1.3727194441808592],[32.487721440328755,1.3727194441808592]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"text","version":85,"versionNonce":629101675,"isDeleted":false,"id":"FMpwST9Y","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":1,"opacity":100,"angle":0,"x":-360.4306936842483,"y":1.4035369096851298,"strokeColor":"#080808","backgroundColor":"transparent","width":421.37994384765625,"height":45,"seed":1479109451,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[{"id":"WOgJaLdyjA3r1I50EH6iv","type":"arrow"}],"updated":1690189055764,"link":null,"locked":false,"fontSize":36,"fontFamily":1,"text":"01       00         0-1","rawText":"01       00         0-1","textAlign":"left","verticalAlign":"top","containerId":null,"originalText":"01       00         0-1","lineHeight":1.25,"baseline":31},{"type":"rectangle","version":151,"versionNonce":1948171205,"isDeleted":false,"id":"a_afVg6_sbveu23dx9rVH","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-392.460807066456,"y":-78.28079312811839,"strokeColor":"#080808","backgroundColor":"transparent","width":564.1877125043668,"height":55.82393104536682,"seed":1940412939,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":{"type":3},"boundElements":[{"id":"WOgJaLdyjA3r1I50EH6iv","type":"arrow"}],"updated":1690189055764,"link":null,"locked":false},{"type":"arrow","version":131,"versionNonce":1863290635,"isDeleted":false,"id":"WOgJaLdyjA3r1I50EH6iv","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-387.88508256786537,"y":-20.169010306474433,"strokeColor":"#080808","backgroundColor":"transparent","width":24.25138382920727,"height":50.3330532686434,"seed":2073367429,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":{"type":2},"boundElements":[],"updated":1690189055765,"link":null,"locked":false,"startBinding":{"elementId":"a_afVg6_sbveu23dx9rVH","focus":0.9882473840302242,"gap":2.2878517762771367},"endBinding":{"elementId":"FMpwST9Y","focus":-0.979138441081352,"gap":3.2030050544098003},"lastCommittedPoint":null,"startArrowhead":null,"endArrowhead":"arrow","points":[[0,0],[24.25138382920727,50.3330532686434]]},{"type":"rectangle","version":93,"versionNonce":1992096549,"isDeleted":false,"id":"BCmFKt1geiU4UyMnvi40f","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-364.0912649047063,"y":-5.984228752581345,"strokeColor":"#080808","backgroundColor":"transparent","width":440.64298347412654,"height":53.07847121096867,"seed":328535979,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":{"type":3},"boundElements":[{"id":"YPJs2PGlcm7ugVUwwVYdw","type":"arrow"}],"updated":1690189055765,"link":null,"locked":false},{"type":"text","version":145,"versionNonce":469726635,"isDeleted":false,"id":"JO66J4lV","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-317.41880380255725,"y":60.2972475127971,"strokeColor":"#080808","backgroundColor":"transparent","width":344.2799072265625,"height":25,"seed":2120529989,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":null,"boundElements":[{"id":"YPJs2PGlcm7ugVUwwVYdw","type":"arrow"}],"updated":1690189055765,"link":null,"locked":false,"fontSize":20,"fontFamily":1,"text":"M12访视时阿托品-M12访视时安慰剂=0","rawText":"M12访视时阿托品-M12访视时安慰剂=0","textAlign":"left","verticalAlign":"top","containerId":null,"originalText":"M12访视时阿托品-M12访视时安慰剂=0","lineHeight":1.25,"baseline":17},{"type":"arrow","version":174,"versionNonce":1042042501,"isDeleted":false,"id":"YPJs2PGlcm7ugVUwwVYdw","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-359.0579742400675,"y":53.042686401158846,"strokeColor":"#080808","backgroundColor":"transparent","width":31.57254721615965,"height":28.8271083277979,"seed":576712645,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":{"type":2},"boundElements":[],"updated":1690189055765,"link":null,"locked":false,"startBinding":{"elementId":"BCmFKt1geiU4UyMnvi40f","focus":1.0059412246114854,"gap":5.948443942771519},"endBinding":{"elementId":"wPqO3XjrdlAMQW68JN7RT","focus":-0.9344543298215957,"gap":1},"lastCommittedPoint":null,"startArrowhead":null,"endArrowhead":"arrow","points":[[0,0],[31.57254721615965,28.8271083277979]]},{"type":"rectangle","version":105,"versionNonce":2071562315,"isDeleted":false,"id":"wPqO3XjrdlAMQW68JN7RT","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-327.02783991182326,"y":58.99115128996684,"strokeColor":"#080808","backgroundColor":"transparent","width":352.7888971544791,"height":35,"seed":128083525,"groupIds":["V5PE8pfAf3uOZGyD4SaBs"],"frameId":null,"roundness":{"type":3},"boundElements":[{"id":"YPJs2PGlcm7ugVUwwVYdw","type":"arrow"}],"updated":1690189055765,"link":null,"locked":false},{"type":"text","version":152,"versionNonce":324552427,"isDeleted":false,"id":"ZyEDe6yq","fillStyle":"hachure","strokeWidth":0.5,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-413.4761973931932,"y":132.79661277272407,"strokeColor":"#080808","backgroundColor":"transparent","width":508.3798828125,"height":75,"seed":1983061003,"groupIds":[],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189055765,"link":null,"locked":false,"fontSize":20,"fontFamily":1,"text":"注意：指定了class avisitn(ref='6')；\n则10 00 -10表示 M12访视时阿托品-M12访视时安慰剂=0\n形式改为1-M12-M6 2-M12-M6 3-M12-M6","rawText":"注意：指定了class avisitn(ref='6')；\n则10 00 -10表示 M12访视时阿托品-M12访视时安慰剂=0\n形式改为1-M12-M6 2-M12-M6 3-M12-M6","textAlign":"left","verticalAlign":"top","containerId":null,"originalText":"注意：指定了class avisitn(ref='6')；\n则10 00 -10表示 M12访视时阿托品-M12访视时安慰剂=0\n形式改为1-M12-M6 2-M12-M6 3-M12-M6","lineHeight":1.25,"baseline":67},{"type":"freedraw","version":50,"versionNonce":1615598533,"isDeleted":false,"id":"HQ-Dnnb6Jsf5Kajnp896G","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-395.91558361648066,"y":178.0845114600354,"strokeColor":"#5c940d","backgroundColor":"transparent","width":97.969740017449,"height":0,"seed":1244017035,"groupIds":[],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189076188,"link":null,"locked":false,"points":[[0,0],[1.848485660706558,0],[9.242428303532904,0],[16.63637094635925,0],[22.18182792847898,0],[27.11113242558082,0],[30.191951261976044,0],[32.65658940770061,0],[36.96971321413167,0],[42.51517019625146,0],[46.212141517664634,0],[49.29296035405986,0],[51.14144601476647,0],[52.98993167547303,0],[54.2222648511617,0],[55.45456982119754,0],[57.91923617257481,0],[59.76772183328143,0],[61.000026803317326,0],[62.232359979005935,0],[63.4646931546946,0],[64.69699812473044,0],[65.31317881540116,0],[67.16166447610777,0],[67.77781696112567,0],[69.62630262183228,0],[70.85863579752095,0],[72.09094076755679,0],[72.7071214582275,0],[75.17175960395201,0],[77.63639774967652,0],[78.86873092536524,0],[80.10106410105385,0],[80.7172165860718,0],[83.1818547317963,0],[84.41418790748497,0],[86.87882605320948,0],[87.4950067438802,0],[88.11115922889815,0],[89.9596448896047,0],[91.80813055031132,0],[93.04046372599993,0],[94.88894938670654,0],[95.50510187172443,0],[96.12125435674244,0],[96.73743504741316,0],[97.969740017449,0],[97.969740017449,0]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"freedraw","version":67,"versionNonce":1523345509,"isDeleted":false,"id":"LiCr-WIGrSfld7CvbidUY","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-334.2993761224926,"y":200.8825200791851,"strokeColor":"#5c940d","backgroundColor":"transparent","width":302.5354676652092,"height":1.232304970035898,"seed":591569317,"groupIds":[],"frameId":null,"roundness":null,"boundElements":[],"updated":1690189072252,"link":null,"locked":false,"points":[[0,0],[1.848485660706558,0],[3.696971321413116,0],[9.858580788550853,0.616180690670717],[17.2525234313772,0.616180690670717],[27.111104219928052,0.616180690670717],[40.666684535544846,0.616180690670717],[56.68687479123338,0.616180690670717],[72.70709325257474,0.616180690670717],[84.41415970183215,0.616180690670717],[94.2727686960358,0.616180690670717],[101.05053064819143,0.616180690670717],[106.59598763031119,0.616180690670717],[112.14144461243092,0.616180690670717],[121.38387291596388,0.616180690670717],[123.84853926734115,0.616180690670717],[130.62630121949678,0.616180690670717],[133.70714826154477,0.616180690670717],[134.93945323158067,0.616180690670717],[137.40411958295795,0.616180690670717],[141.10109090437112,0.616180690670717],[147.26267216585603,0.616180690670717],[155.2727954993531,0.616180690670717],[162.66673814217944,0.616180690670717],[169.44450009433507,0.616180690670717],[173.75765210641896,0.616180690670717],[179.9192897792094,0.616180690670717],[183.00008040995186,0.616180690670717],[192.24250871348482,0.616180690670717],[195.939480034898,0.616180690670717],[203.3334226777243,0.616180690670717],[205.79808902910162,0.616180690670717],[212.57585098125725,0.616180690670717],[217.50512727270626,0.616180690670717],[223.05058425482605,0.616180690670717],[228.59604123694578,0.616180690670717],[232.90919324902967,0.616180690670717],[236.60616457044284,0.616180690670717],[238.4546502311494,0.616180690670717],[240.91931658252673,0.616180690670717],[247.08089784401164,0.616180690670717],[248.9293835047182,0.616180690670717],[255.0910211775087,0.616180690670717],[256.32332614754455,0.616180690670717],[258.7879924989219,0.616180690670717],[260.0202974689577,0.616180690670717],[266.7981158324189,0.616180690670717],[274.19205847524523,0.616180690670717],[279.737515457365,0.616180690670717],[281.5860011180716,0.616180690670717],[282.8183060881075,0.616180690670717],[284.66679174881403,0.616180690670717],[285.89909671884993,0.616180690670717],[287.13145810019137,0.616180690670717],[288.3637630702272,0.616180690670717],[291.44455370096966,0.616180690670717],[292.0607343916404,0.616180690670717],[292.6769150823111,0.616180690670717],[294.5254007430177,1.232304970035898],[298.2223720644309,1.232304970035898],[301.3031626951733,1.232304970035898],[302.5354676652092,1.232304970035898],[302.5354676652092,1.232304970035898]],"lastCommittedPoint":null,"simulatePressure":true,"pressures":[]},{"type":"rectangle","version":74,"versionNonce":713779205,"isDeleted":false,"id":"B-Kztc5dqMRUUQOF9saQ2","fillStyle":"hachure","strokeWidth":1,"strokeStyle":"solid","roughness":2,"opacity":100,"angle":0,"x":-429.8044779942171,"y":124.47842729954442,"strokeColor":"#960d0d","backgroundColor":"transparent","width":545.3032981140954,"height":92.42428303532927,"seed":1429118379,"groupIds":[],"frameId":null,"roundness":{"type":3},"boundElements":[],"updated":1690189104642,"link":null,"locked":false}],"appState":{"theme":"light","viewBackgroundColor":"#ffffff","currentItemStrokeColor":"#960d0d","currentItemBackgroundColor":"transparent","currentItemFillStyle":"hachure","currentItemStrokeWidth":1,"currentItemStrokeStyle":"solid","currentItemRoughness":2,"currentItemOpacity":100,"currentItemFontFamily":1,"currentItemFontSize":20,"currentItemTextAlign":"left","currentItemStartArrowhead":null,"currentItemEndArrowhead":"arrow","scrollX":451.7489998078854,"scrollY":160.07476555078557,"zoom":{"value":1.2000000000000002},"currentItemRoundness":"round","gridSize":null,"gridColor":{"Bold":"#C9C9C9FF","Regular":"#EDEDEDFF"},"colorPalette":{},"currentStrokeOptions":null,"previousGridSize":null,"frameRendering":{"enabled":true,"clip":true,"name":true,"outline":true}},"files":{}};InitialData.scrollToContent=true;App=()=>{const e=React.useRef(null),t=React.useRef(null),[n,i]=React.useState({width:void 0,height:void 0});return React.useEffect(()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height});const e=()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height})};return window.addEventListener("resize",e),()=>window.removeEventListener("resize",e)},[t]),React.createElement(React.Fragment,null,React.createElement("div",{className:"excalidraw-wrapper",ref:t},React.createElement(ExcalidrawLib.Excalidraw,{ref:e,width:n.width,height:n.height,initialData:InitialData,viewModeEnabled:!0,zenModeEnabled:!0,gridModeEnabled:!1})))},excalidrawWrapper=document.getElementById("交互作用的estimateexcalidraw.md1");ReactDOM.render(React.createElement(App),excalidrawWrapper);})();</script>

`注： 如上图所示红框中，class添加ref后，对estimate中系数有一定影响`：





