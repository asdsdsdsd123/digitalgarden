---
{"dg-publish":true,"dg-home":null,"permalink":"/05 R&VBA&PS&PROJECT/VBA/02 WORD&VBA操作rtf&doc&docx/","dgPassFrontmatter":true}
---

## 1. WORD VBA 批掉去掉rtf页码

```vba
Sub 批量删除页眉页脚
'此代码功能为列出指定文件夹中所有选取的WORD文件全路径名 
Dim myDialog As FileDialog, oDoc As Document, oSec As Section 
Dim oFile As Variant, myRange As Range 
On Error Resume Next 
'定义一个文件夹选取对话框 
Set myDialog = Application.FileDialog(msoFileDialogFilePicker) 
With myDialog 
	.Filters.Clear '清除所有文件筛选器中的项目 
	.Filters.Add "所有Word文件", "*.doc,*.docx", 1 '增加筛选器的项目为所有Word文件
	.AllowMultiSelect = True '允许多项选择 
	If .Show = -1 Then '确定 
	For Each oFile In .SelectedItems '在所有选取项目中循环 
		Set oDoc = Word.Documents.Open(FileName:=oFile, Visible:=False) 
		For Each oSec In oDoc.Sections '文档的节中循环 
			Set myRange = oSec.Headers(wdHeaderFooterPrimary).Range 
			myRange.Delete '删除页眉中的内容 
			myRange.ParagraphFormat.Borders(wdBorderBottom).LineStyle = wdLineStyleNone '段落 下边框线 
			Set myRange = oSec.Footers(wdHeaderFooterPrimary).Range
			myRange.Delete '删除页脚中的内容 
		Next 
		oDoc.Close True 
	Next 
	End If
End With 
End Sub
```

## 2. WORD VBA检查空白页

### 2.1. 检查是否存在空白页

````ad-note
collapse: close
title: 时间比较长，不推荐，建议用下面的代码
```vba

Sub CheckBlankPages()
    Dim finalCount As Integer
    Dim pgCount As Integer
    Dim rng As Range
    Dim fnd As Find
    Dim fl as Integer
    
    Set rng = ActiveDocument.Content
    rng.Select '将光标移动到文档的开头
    fl=inputbox("输入program: l-")
    Set fnd = rng.Find
    With fnd
        .Text = "final"
        Do While .Execute
            finalCount = finalCount + 1 '统计匹配项数量
        Loop
    End With
    
    pgCount = ActiveDocument.Content.ComputeStatistics(wdStatisticPages) '文档总共页码数
    
    If finalCount = pgCount Then
        MsgBox "页码一致"
    Else
        MsgBox "有空白页需检查"
    End If
    
End Sub

```
````

```vba

Sub CheckBlankPages()
    Dim pgCount As Integer
    Dim rng As Range
    Dim fl As Integer
    
    Set rng = ActiveDocument.Content
    rng.Select '将光标移动到文档的开头
	
    fl=inputbox("输出find program: l-xxx所找到的字符数")
    pgCount = ActiveDocument.Content.ComputeStatistics(wdStatisticPages) '文档总共页码数
    
    If finalCount = pgCount Then
        MsgBox "页码没问题"
    Else
        MsgBox "有空白页" & "文档页码为：" & pgCount & "，应该为：" & fl
    End If
    
End Sub

```


### 2.2. 输出空白页页码

```vba

Sub CheckPagesSpec()
    Dim pgCount2 As Integer
    Dim rng2 As Range
    Dim i As Integer
    Dim pageList As String
    
    Set rng2 = ActiveDocument.Content
    pgCount2 = ActiveDocument.Content.ComputeStatistics(wdStatisticPages) '文档总共页码数
    
    For i = 1 To pgCount2
        If rng2.Information(wdActiveEndPageNumber) = i Then
            If InStr(rng2.Paragraphs(1).Range.Text, "列表") = 0 Then '判断第一段是否不包含“列表”文本
                pageList = pageList & i & ", " '将不包含列表的页的页码存入字符串中
            End If
        End If
        Set rng2 = rng2.GoTo(wdGoToPage, wdGoToAbsolute, i + 1) '将当前范围设置为下一页的开头
    Next i
    
    If pageList = "" Then
        MsgBox "All pages contain a list in the first paragraph or are blank."
    Else
        MsgBox "The following pages are either blank or do not contain a list in the first paragraph: " & Left(pageList, Len(pageList) - 2) & "."
    End If
    
End Sub

```

### 2.3. 输出空白页页码-先指定打开要操作的文件

```vba

Sub CheckPagesForBlankOrNonListFirstLine()
    Dim fd As FileDialog
    Dim filePath As String
    Dim pgCount2 As Integer
    Dim rng2 As Range
    Dim i As Integer
    Dim foundPage As Boolean '用于跟踪找到不包含“列表”文本的页的状态
    Dim cnen As String
    Dim pgCount As Integer
    Dim rng As Range
    Dim fl As Integer
    
    Set fd = Application.FileDialog(msoFileDialogOpen)
    With fd
        .Filters.Clear
        .Filters.Add "RTF 文件", "*.rtf"
        .Title = "请选择要操作的 RTF 文件"
        .InitialFileName = "C:\"
        If .Show = -1 Then
            filePath = .SelectedItems(1)
        Else
            MsgBox "您取消了选择文件。"
            Exit Sub
        End If
    End With
    
    Documents.Open FileName:=filePath, Format:=wdOpenFormatRTF '打开 RTF 文件

	Set rng = ActiveDocument.Content
    rng.Select '将光标移动到文档的开头
	
    fl=inputbox("输出find program: l-xxx所找到的字符数")
    pgCount = ActiveDocument.Content.ComputeStatistics(wdStatisticPages) '文档总共页码数
    
    If finalCount = pgCount Then
        MsgBox "页码没问题"
    Else
        MsgBox "有空白页" & "文档页码为：" & pgCount & "，应该为：" & fl
        '后面具体哪页比较卡，不如用sas判断
        cnen = InputBox("请输入rtf第一行包含字符")
	    For i = 1 To pgCount
	        If rng.Information(wdActiveEndPageNumber) = i Then
	            If InStr(rng.Paragraphs(1).Range.Text, cnen) = 0 And Not foundPage Then '如果不包含“列表”文本且状态为未找到
	                foundPage = True '将状态标记为已找到
	                MsgBox "第 " & i & " 页:空白页需要检查"
	                Exit For '退出循环
	            End If
	        End If
	        Set rng = rng.GoTo(wdGoToPage, wdGoToAbsolute, i + 1) '将当前范围设置为下一页的开头
	    Next i
    End If
    
    ActiveDocument.Close SaveChanges:=False '关闭 RTF 文件，不保存更改
End Sub

```

## 3. WORD VBA获取SAP的标题及脚注

### 3.1. 删除所有表格

表格中的字样也在paragraph中，影响后续的遍历

```vba
Sub DeleteTables()
    Word.Application.DisplayAlerts = False
    Dim tbl As table
    Dim newDoc As Document
    
    ' 删除文档中的所有表格
    For Each tbl In ActiveDocument.Tables
        tbl.Delete
    Next tbl
    
End Sub
```

### 3.2. 获取所有文本的样式

思路：根据格式确定title/footnote
但word格式不一定，需要调整（如title部分有正文样式，需要改）

```vba

Sub OutputSTYLE()

    Dim pg As Paragraph, r As Range, i As Integer, col As Integer, _
       filepath As String
    
    Dim xlApp As Object
    Dim xlBook As Object
    Dim xlSheet As Object
    
    ' 新建 Excel 应用程序
    Set xlApp = CreateObject("Excel.Application")
    xlApp.Visible = True
    
    ' 打开 Excel 文件
    filepath = InputBox("输出位置？" & Chr(12) & Chr(13) & _
     "注：输出名字为outstyle.xlsx,需检查输出位置是否存在该文件")
     
    Set xlBook = xlApp.Workbooks.Open(filepath & "\" & "outstyle.xlsx")
        ' 替换为您的 Excel 文件路径
    
    ' 选择第一个工作表
    Set xlSheet = xlBook.Worksheets(1)
    
    i = 0

    For Each pg In ActiveDocument.Paragraphs
         pgtext = pg.Range.Text
        If Asc(pgtext) <> 13 And Asc(pgtext) <> 12 _
            Then
            i = i + 1
            xlBook.Worksheets(1).Cells(i, 1).Value = pgtext
            xlBook.Worksheets(1).Cells(i, 2).Value = pg.Style
        End If
           
    Next
End Sub

```

确认标题的样式都哪些
### 3.3. 遍历每一段

- 遍历每一段，确认删除了表格后，修改样式后：
	- title是有固定的几种样式，确认和footnote不重合
	- footnote是标题样式之外的样式。
- 其余要求见注意事项 

```vba
Sub OutputTOC()

    Dim pg As Paragraph, r As Range, i As Integer, col As Integer, _
        filepath As String
    
    Dim xlApp As Object
    Dim xlBook As Object
    Dim xlSheet As Object
    
    ' 新建 Excel 应用程序
    Set xlApp = CreateObject("Excel.Application")
    xlApp.Visible = True
    
    ' 打开 Excel 文件
    filepath = InputBox("输出位置？" & Chr(12) & Chr(13) & _
     "注：输出名字为outtoc.xlsx,需检查输出位置是否存在该文件")
     
    Set xlBook = xlApp.Workbooks.Open(filepath & "outtoc.xlsx")
        ' 替换为您的 Excel 文件路径
    
    ' 选择第一个工作表
    Set xlSheet = xlBook.Worksheets(1)
    
    i = 0

    For Each pg In ActiveDocument.Paragraphs
        
        pgtext = pg.Range.Text
        If (pg.Style = "level2" Or pg.Style = "表格标题" Or pg.Style = "标题 3") _
                    And Asc(pgtext) <> 13 And Asc(pgtext) <> 12 _
            Then
            i = i + 1
            col = 1
            xlBook.Worksheets(1).Cells(i, col).Value = pgtext
                
            '这里操作标题
           
            ElseIf pg.Style <> tittle_style And Asc(pgtext) <> 13 And Asc(pgtext) <> 12 _
                Then
                col = col + 1
                xlBook.Worksheets(1).Cells(i, col).Value = pgtext
                    '这里操作脚注
        End If
        pgtext = ""
    Next
End Sub

```

> [!note]+  ***注意事项***
> - ***注意每个脚注后不要有任何的形式，整体格式规整，方便读取***
> 	- ![../../Z appendix/Pasted image 20230421233248.png|775](/img/user/Z%20appendix/Pasted%20image%2020230421233248.png)
> - ***目前统一规则标题是标题 2的样式，需要检查每个表示是否是这种格式***
> 	- ![../../Z appendix/Pasted image 20230421233637.png|750](/img/user/Z%20appendix/Pasted%20image%2020230421233637.png)
> - ***脚注带自动编号的(如[1])，可能会导致此行不会输出文本，需要自行转化成文本非自动编号*** 
> 	- ![../../Z appendix/Pasted image 20230422152818.png](/img/user/Z%20appendix/Pasted%20image%2020230422152818.png)
> - ***输出的excel中，有回车换行符号，需要excel-vba或SAS处理：***
> ```VBA
> Sub RemoveLineBreaks()
> 	For Each Cell In Selection
> 	     Cell.Value = Replace(Replace(Cell.Value, Chr(10), vbNullString), Chr(13), vbNullString)
> 	Next
> End Sub

### 3.4. check
将SAP中目录粘贴过来，显示输出不等的地方，检查是否有遗漏表格等

### 3.5. sas程序处理 

```sas
proc format;
	value $type
		"t"="Table"
		"l"="Listing"
		"f"="Figure";
run;
%let _file=V:\Telix\TLX250-CDx-CP-001\BS\csr\user\cuifan\statistics;
/*读取VBA输出的excel*/
proc import datafile="&_file.\outtoc.xlsx" out=_toc dbms=xlsx replace;
	getnames=n;
run;

data toc;
	set _toc;
	array oraw[*] _all_;
	do _i=1 to dim(oraw);

		oraw[_i]=tranwrd(tranwrd(tranwrd(oraw[_i],"_x000D_",""),"_x000C_",""),"09"x,"");/*此步骤只去除字符，不增加字符，会默认超过长度截断*/
		if find(oraw[_i],"<Test Name>") or
			(find(oraw[_i],"Repeat the following") and find(oraw[_i],"according to"))  then oraw[_i]="";

		if  find(oraw[_i],"Programming notes:") or  find(oraw[_i],"Programming note:") 
			then flag=_i;
		if _i>=flag>. then oraw[_i]="";
	end;
run;

data toc2;
/*	retain tittle1-tittle7 footnote1-footnote9 _tittle1 tlf filename ;*/
	length tittle1-tittle7  $200 footnote1-footnote9  a b c d e f g h i j k $2000 
		toc $32767 _tittle1 $200 num1-num10 filename $200;
	set toc;

	format _all_;
	informat _all_;

	toc=catx("|||||",b,c,d,e,f,g,h,i,j,k);
	array ft footnote1-footnote9;
	do _ii=1 to countw(toc,"|||||");
		ft[_ii]=kscan(toc,_ii,"|||||");
		ft[_ii]=tranwrd(tranwrd(tranwrd(ft[_ii],"%","@{unicode 0025}"),"89Zr","@{super 89}Zr"),"Zr89","@{super 89}Zr"); /*该步骤做字符替换*/
	end;

	tittle1=a;
	call missing(of tittle2-tittle7);
	if find(kscan(tittle1,1," "),"Table") then _tlf="t";
	if find(kscan(tittle1,1," "),"Listing") then _tlf="l";
	if find(kscan(tittle1,1," "),"Figure") then _tlf="f";
	_tittle1=kscan(substr(tittle1,anydigit(tittle1)),1," ");
	if _tittle1="" or compress(_tittle1,".","d") ne "" then put "WARNING: 格式不对，请检查，n=" _n_; 

	array num[*] $200 num1-num10;
	do _iii=1 to countw(_tittle1,".");
		num[_iii]=put(input(kscan(_tittle1,_iii,".."),??best.),z2.);
	end;

	filename=catx("-",_tlf,num1,num2,num3,num4,num5,num6,num7,num8,num9,num10)||"-";

	drop a--k;
run;

/*输出tracker*/

data tracker;
	length a tlftype $20  u_r $1  _tittle1 rtftt1 b filename   c d e f g $200 _tlf title1  $1000 
		   num1-num10  $200;
	set _toc(rename=(a=tittle1) keep=a);
	title1=tittle1;
	if title1 ne "";
	n=_n_;

	if prxmatch("/表|table/",lowcase(kscan(title1,1," "))) then _tlf="t";
	if prxmatch("/列表|listing/",lowcase(kscan(title1,1," "))) then _tlf="l";
	if prxmatch("/图|figure/",lowcase(kscan(title1,1," "))) then _tlf="f";
	_tittle1=kscan(substr(title1,anydigit(title1)),1," ");
	if _tittle1="" or compress(_tittle1,".","d") ne "" then put "WARNING: 格式不对，请检查，n=" n;  /*调整test数据集或metadat*/

	array num[*] $200 num1-num10;
	do _iii=1 to countw(_tittle1,".");
		num[_iii]=put(input(kscan(_tittle1,_iii,".."),??best.),z2.);
	end;

	filename=catx("-",_tlf,num1,num2,num3,num4,num5,num6,num7,num8,num9,num10)||"-";
	rtftt1=strip(ksubstr(title1,kfind(title1," ",10)+1)); /*中英文这里10的数值可能不同，中文表xxx，和tablexxxx占的字符数不同*/
	a="Dry Run";u_r="";call missing(b,c,d,e,f,g);
	tlftype=put(_tlf, $type.);
	keep title1 filename tlftype  _tittle1 rtftt1 _tlf a b c d e f g u_r; 
	label a="Deliverable"  tlftype="Type" u_r="U/R"   _tittle1="Output ID" rtftt1="Output Title" b="Dev Prog" filename="Rtf name" c=suffix_prog d=suffix_rtf ;

run;

/*输出Header footnote*/
data tocfinal;
	retain type pgmname pgmid dataset filename tittle1-tittle7 footnote1-footnote9 _prefix;
	length type pgmname dataset _tlf _prefix $200;
	set toc2;
	type=put(_tlf,$type.);
	call missing(pgmname, pgmid, dataset,_prefix);
	pgmname=cats(lowcase(_tlf),"-");
	keep type pgmname pgmid dataset filename tittle1-tittle7 footnote1-footnote9 _prefix;
run;

/*output xlsx*/
proc export data=tracker outfile="&_file.\toc_tracker.xlsx" dbms=xlsx replace label;
	sheet="tracker";
run;

proc export data=tocfinal outfile="&_file.\toc_tracker.xlsx" dbms=xlsx replace;
	sheet="toc";
run;

```

*输出文件先写好_prefix，每个不同*。
	结合EXCEL的CONCAT函数 --> 连接filename与_prefix，连接pgmname与_prefix。连接pgmname与_prefix。
	再手动调整pgmname。多个结构相同的表，名称用一个pgmname，filename每个文件不同
	再根据pgmname，用函数输出qcpgnam。**=CONCAT(O3,MID(G3,3,LEN(G3)-2))**

## 4. 核对两份Word文档内容差异

![|750](https://www.officeday.cn/wp-content/uploads/2021/07/ChMkKmC9uhCITl9cAAHshQi_M_wAAQMTwINHrQAAeyd853.jpg)

- 先打开修改前的原版 Word 文档。点击菜单栏：审阅—比较。
- 在设置界面，原文档处点击边上的文件夹，选择我们的之收集的文档初稿；
	- 修订的文档处点击边上的文件夹，选择我们的修改后 Word 版本内容

![|700](https://www.officeday.cn/wp-content/uploads/2021/07/ChMkKWC9uiOIIMiXAAtMxiIoWYQAAQMTwMAQMgAC0ze453.gif)


- 通过上面比较文档的设置后，下方点击更多我们可以查看到需要对比的内容，这里我们默认系统的设置。点击确定就可以进入 Word 文档对比的窗口的界面。如下图所示：
	- 左边第一部分为两份文档有差别的具体内容文字；
	- 中间第二部分为修改后的版本；
	- 右边第三部分上面为原文档内容。这样我们就能够清晰的看的数据差异的部分。

![|800](https://www.officeday.cn/wp-content/uploads/2021/07/ChMkKmC9uiiIMV8HAAJgbaqbD4oAAQMTwMVkWkAAmCF198.jpg)


## 5. 输出word中批注到excel

```vba
Sub ExportCommentsToExcel()
    Dim wdApp As Object
    Dim wdDoc As Object
    Dim wdComment As Object
    Dim xlApp As Object
    Dim xlBook As Object
    Dim xlSheet As Object
    Dim i As Integer
    ' 新建 Excel 应用程序
    Set xlApp = CreateObject("Excel.Application")
    xlApp.Visible = True
    
    ' 打开 Excel 文件
    filepath = InputBox("输出位置？" & Chr(12) & Chr(13) & _
     "注：输出名字为com.xlsx,需检查输出位置是否存在该文件")
     
    Set xlBook = xlApp.Workbooks.Open(filepath & "\com.xlsx")
        ' 替换为您的 Excel 文件路径
    
    ' 选择第一个工作表
    Set xlSheet = xlBook.Worksheets(1)
    
    ' 在Excel表格中添加标题行
    xlSheet.Cells(1, 1).Value = "作者"
    xlSheet.Cells(1, 2).Value = "批注内容"
    xlSheet.Cells(1, 3).Value = "页码"
    
    ' 循环遍历Word文档中的每个批注，将其导出到Excel表格中
    i = 2
    For Each wdComment In ActiveDocument.Comments
        xlSheet.Cells(i, 1).Value = wdComment.Author
        xlSheet.Cells(i, 2).Value = wdComment.Range.Text
        xlSheet.Cells(i, 3).Value = wdComment.Scope.Information(wdActiveEndPageNumber)
        i = i + 1
    Next wdComment
    
    
End Sub
```

