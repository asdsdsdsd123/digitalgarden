---
{"date":"2023/10/19","parent":"telix sap","collections":["07 备忘同步"],"version":1785,"libraryID":1,"itemKey":"8Q5IHDXJ","dg-publish":true,"permalink":"/03 STAT/Telix SAP/","dgPassFrontmatter":true}
---

# Telix SAP

## 1. 编码版本信息

*   MedDRA 25.1
*   “WHODrug Global Sep 1 2022

## 2. pk分析

*   分析集：PKS，***<span style="color: #ff2020">给药后计划访视内有检测浓度。</span>***

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%223%22%2C%22position%22%3A%7B%22pageIndex%22%3A2%2C%22rects%22%3A%5B%5B429.597%2C189.533%2C541.872%2C200.944%5D%2C%5B90.525%2C174.374%2C541.953%2C185.286%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=3"><span style="background-color: #ffd40080">“received 89Zr-TLX250 injection, and had a sufficient number of timepoints collected and the concentration detected”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 3</a></span>)</span></zcitation></span>

    <span style="background-color: #ff666680">FYI (as per page 66 of protocol):</span>

    ![\<img alt="" data-attachment-key="YCMCR375" width="575" height="121" src="attachments/YCMCR375.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/YCMCR375.png)

*   PK浓度小数位数：

    *   PK浓度按照有效位数记录：显示为相同的有效数字，否则按照上标的小数位数显示
    *   %CV显示1位小数位数，其余显示为3个有效数字

*   ***<span style="color: #ff2020">PK参数小数位数？是否同上（看数据）</span>***

### 2.1. PC

*   给药后PC浓度低于LLOQ，<span style="color: #ff2020">置为缺失</span>，BLQ flag填充；给药前C浓度低于LLOQ，<span style="color: #ff2020">置为0。</span>

*   PK个体图用实际时间，平均图用ATPT

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%2210%22%2C%22position%22%3A%7B%22pageIndex%22%3A9%2C%22rects%22%3A%5B%5B381.994%2C212.804%2C541.594%2C223.716%5D%2C%5B54.475%2C196.804%2C541.106%2C207.716%5D%2C%5B54.475%2C179.984%2C325.614%2C190.896%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2210%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=10"><span style="background-color: #ffd40080">“Individual subject and summary profiles (mean) of the whole blood concentration-time data will be plotted using actual times (individual) and nominal times (summary), respectively.”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2210%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 10</a></span>)</span></zcitation></span>

### 2.2. PP

*   ***<span style="color: #ff2020">排除异常值，异常值不做table，只列listing。</span>***

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%2211%22%2C%22position%22%3A%7B%22pageIndex%22%3A10%2C%22rects%22%3A%5B%5B359.059%2C223.234%2C540.247%2C234.146%5D%2C%5B54.475%2C207.204%2C421.375%2C218.116%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2211%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=11"><span style="background-color: #ffd40080">“Outliers are defined as values that are less than 5% of the geometric mean of all the values in that group of patients”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2211%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 11</a></span>)</span></zcitation></span>

## 3. 剂量学分析

<span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%2211%22%2C%22position%22%3A%7B%22pageIndex%22%3A10%2C%22rects%22%3A%5B%5B83.3%2C160.58%2C218%2C172.033%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2211%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=11"><span style="background-color: #ffd40080">“DOSIMETRYANALYSIS”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2211%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 11</a></span>)</span></zcitation></span>

*   ***<span style="color: #ff2020">分析集：PPS，给药后有剂量学数据。ADAM Spec不太一样？</span>***

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%223%22%2C%22position%22%3A%7B%22pageIndex%22%3A2%2C%22rects%22%3A%5B%5B90.525%2C244.813%2C541.772%2C256.194%5D%2C%5B90.525%2C228.824%2C540.885%2C239.736%5D%2C%5B90.525%2C213.604%2C179.925%2C224.516%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=3"><span style="background-color: #ffd40080">“Per-protocol Analysis Set (PPS): includes all subjects who received 89Zr-TLX250 injection and had a sufficient number of dosimetry timepoints available for evaluation without significant protocol violations.”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 3</a></span>)</span></zcitation></span>

*   ***<span style="color: #ff2020">小数位数惯例就好，不用按照有效数字来？(看数据)</span>***

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%223%22%2C%22position%22%3A%7B%22pageIndex%22%3A2%2C%22rects%22%3A%5B%5B161.83%2C683.654%2C434.374%2C694.374%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=3"><span style="background-color: #ffd40080">“Table 1 Decimal Places of Basic Descriptive Statistics”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%223%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 3</a></span>)</span></zcitation></span>

*   这部分数据由外部文件来，根据目前的TLFs，需要有以下内容

    *   <span style="background-color: #a28ae580">Organ </span>*<span style="color: #7e8386">%Injected Activity</span>* for 89Zr-girentuximab**（%Injected Activity = Activity / Administered Activity）**

    *   Normalized *<span style="color: #05a2ef">Absorbed Dose</span>* to <span style="background-color: #a28ae580">Organs </span>for 89Zr-girentuximab calculated<span style="color: #ff2020"> by IDAC-Dose 2.1/OLINDA/EXM 2.2</span>

        *   表展示每个<span style="background-color: #a28ae580">organ</span>的吸收剂量与软件计算的来的effective dose calculation（单位(mSv/MBq）

        *   IDAC Dose and Olinda are used to ***<span style="color: #eb52f7">calculate the absorbed dose</span>***。

    *   <span style="background-color: #aaaaaa80">Tumor </span>

        *<span style="color: #7e8386">%Injected Activity</span>* for 89Zr-girentuximab，同前

    *   <span style="background-color: #aaaaaa80">Tumor </span>

        *<span style="color: #05a2ef">Absorbed Dose</span>* (UnitmGy) for 89Zr-girentuximab，同前

*   和PC一样，画图个体图用实际时点，平均图用ATPT

    <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%2212%22%2C%22position%22%3A%7B%22pageIndex%22%3A11%2C%22rects%22%3A%5B%5B54.475%2C542.724%2C541.23%2C553.636%5D%2C%5B54.475%2C526.704%2C541.666%2C537.616%5D%2C%5B54.475%2C510.704%2C139.831%2C521.616%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=12"><span style="background-color: #ffd40080">“A figure of the organ activity vs. actual imaging times will be plotted for each patient. Additionally mean organ activity vs. nominal PET imaging time (excluding outliers) will be plotted, with error bars and a curve fitted.”</span></a></span><span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 12</a></span>)</span></zcitation></span>

### 3.1. radiation dosimetry

上面的<span style="background-color: #a28ae580">organ</span>分

*   这个表的没有在SAP中找到相应描述

    *   ![\<img alt="" data-attachment-key="4BW2FDDM" width="1251" height="392" src="attachments/4BW2FDDM.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/4BW2FDDM.png)

<!---->

*   “**normalized absorbed dose (mGy/MBq)** for a number of target organs and the remainder of the total body” <span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 12</a></span>)</span> 🔤一些靶器官和全身其余部分的归一化吸收剂量 (mGy/MBq)🔤

    *   ![\<img alt="" data-attachment-key="Y6W87PNT" width="1333" height="435" src="attachments/Y6W87PNT.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/Y6W87PNT.png)

*   <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22annotationKey%22%3A%223XLZZKHK%22%2C%22color%22%3A%22%23ffd400%22%2C%22pageLabel%22%3A%2212%22%2C%22position%22%3A%7B%22pageIndex%22%3A11%2C%22rects%22%3A%5B%5B343.269%2C641.183%2C415.077%2C652.136%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=12&#x26;annotation=3XLZZKHK">“<strong>effective doses</strong>”</a></span> <span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 12</a></span>)</span> 🔤有效剂量🔤

    *   ![\<img alt="" data-attachment-key="7H7NARGI" width="1230" height="387" src="attachments/7H7NARGI.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/7H7NARGI.png)

### 3.2. tumor dosimetry

上面的<span style="background-color: #aaaaaa80">tumor</span>分析

*   这个表的没有在SAP中找到相应描述

    *   ![\<img alt="" data-attachment-key="HNMRAJHN" width="1184" height="530" src="attachments/HNMRAJHN.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/HNMRAJHN.png)

<!---->

*   <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22pageLabel%22%3A%2212%22%2C%22position%22%3A%7B%22pageIndex%22%3A11%2C%22rects%22%3A%5B%5B78.5%2C372.154%2C213.47%2C383.494%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=12">“renal <strong><em>tumor absorbed dose</em></strong>”</a></span> <span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 12</a></span>)</span>

    *   ![\<img alt="" data-attachment-key="JH6BUF6K" width="1228" height="440" src="attachments/JH6BUF6K.png" ztype="zimage">](/img/user/Z Files for obsidian/attachments/JH6BUF6K.png)

    <!---->

    *   ***<span style="color: #ff2020">肾外病灶的是否分析？目前看Mock up是分析了。</span>***

        *   <span class="highlight" data-annotation="%7B%22attachmentURI%22%3A%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FU4UWHL95%22%2C%22annotationKey%22%3A%226CQPMNTH%22%2C%22color%22%3A%22%23ffd400%22%2C%22pageLabel%22%3A%2212%22%2C%22position%22%3A%7B%22pageIndex%22%3A11%2C%22rects%22%3A%5B%5B54.475%2C332.924%2C542.326%2C344.264%5D%2C%5B54.475%2C316.904%2C340.687%2C327.816%5D%5D%7D%2C%22citationItem%22%3A%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%7D" ztype="zhighlight"><a href="zotero://open-pdf/library/items/U4UWHL95?page=12&#x26;annotation=6CQPMNTH"><span style="background-color: #ffd40080">“If more than 3 extrarenal lesions are found from all subjects, then extrarenal tumour dose (Gy) of 89Zr- girentuximab will be summarized with descriptive statistics”</span></a></span>

            <span style="background-color: #ffd40080"> <zcitation><span class="citation" data-citation="%7B%22citationItems%22%3A%5B%7B%22uris%22%3A%5B%22http%3A%2F%2Fzotero.org%2Fusers%2F8500793%2Fitems%2FJWMPGJU4%22%5D%2C%22locator%22%3A%2212%22%7D%5D%2C%22properties%22%3A%7B%7D%7D" ztype="zcitation">(<span class="citation-item"><a href="zotero://select/library/items/JWMPGJU4">“telix sap”, p. 12</a></span>)</span></zcitation> 🔤如果所有受试者均发现超过 3 个肾外病变，则对 89Zr-girentuximab 的肾外肿瘤剂量 (Gy) 进行描述性统计总结🔤</span>
