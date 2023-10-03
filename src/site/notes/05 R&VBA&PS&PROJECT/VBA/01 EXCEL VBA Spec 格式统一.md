---
{"dg-publish":true,"dg-home":null,"permalink":"/05 R&VBA&PS&PROJECT/VBA/01 EXCEL VBA Spec 格式统一/","dgPassFrontmatter":true}
---


## 1. SDTM Spec检查

```vba
Sub cont()
    Dim r1, r, c, cel, judge, i, r0, cell_last, cmax1, cmax, rmax, r2, cel2
    For Each i In Worksheets
        i.Activate
        rmax = i.Range("A" & Rows.Count).End(xlUp).Row
        cmax = i.Range("Z1").End(xlToLeft).Column

    
        Set judge = i.Range("A2:C11")
        
        If i.Name = "CONTENT" Then  '对于spec的content表里，规范格式
                r = 7
            ElseIf i.Range("A12") = "" And i.Range("A13") <> "" And i.Range("B13") <> "" And _ 
				Range("A2:C2").MergeCells = True And Range("A3:C3").MergeCells = True And Range("A4:C4").MergeCells = True _ 
	            And Range("A5:C5").MergeCells = True And Range("A6:C6").MergeCells = True And Range("A7:C7").MergeCells = True _ 
	            And Range("A8:C8").MergeCells = True And Range("A9:C9").MergeCells = True And Range("A10:C10").MergeCells = True And Range("A11:C11").MergeCells = True _ 
		    Then
                r = 14
            Else: r = 2
                 
        End If
        r0 = r
        
        'MsgBox "表单是：" & i.Name & ",开始行数是：" & r
        c = 1
        Do While i.Range("A" & r) <> ""   '计算多少行，比实际多加1
            Range("A" & r).EntireRow.Select '选中该整行
            Selection.Interior.Pattern = xlNone '把整行填充颜色去掉
            
            If Cells(r, 3) = "数值型" Then Cells(r, 4).Value = 8 '把数据类型为数值型的，赋值长度为8.有的没赋值，为统一，统一赋值
            r = r + 1
        Loop
        
        Do While Cells(r0 - 1, c) <> "" '计算多少列,比实际多加1。
            c = c + 1
            
        Loop
        
        'MsgBox "有效区域是：第" & r & "行，第" & c & "列"
        If r0 <> 2 Then
            Set r1 = Range(Cells(r0, 1), Cells(r - 1, c - 1))
            
                
              '选中有效的数据
            r1.Select
            Selection.Font.Name = "宋体"
            Selection.Font.Name = "Times New Roman"
            Selection.Font.Size = 8
            Selection.Font.Underline = xlUnderlineStyleNone '字体下划线
            Selection.Borders.LineStyle = xlContinuous '框线
            Selection.Font.ColorIndex = xlAutomatic '字体颜色自动
            
            'SDTM标准domain，前面2-11行，统一字体及大小
            If r0 = 14 Then
                i.Range("D2:H11").Font.Name = "宋体"
                i.Range("D2:H11").Font.Name = "Times New Roman"
                i.Range("D2:H11").Font.Size = 8
                i.Range("D2:H11").Font.ColorIndex = xlAutomatic
            End If
             
            
            For Each cel In r1     '遍历每个cell，设置。除超链接，其余字体颜色为自动。超链接字体蓝色，加下划线
                cel.Select
                
                If (r0 = 14 And InStr(i.Name, "SUPP") = 0 And i.Name <> "CO" And (InStr(cel.Address, "E") Or InStr(cel.Address, "H")) And cel.HasFormula = True _
                    And (cel.Address <> "$H$14" And cel.Address <> "$H$15" And cel.Address <> "$H$16" And cel.Address <> "$H$17")) Or _
                        (i.Name = "CONTENT" And InStr(cel.Address, "$A$")) Or _
                            (i.Name = "CO" And cel <> "COSEQ" And cel.HasFormula = True And (InStr(cel.Address, "E") Or InStr(cel.Address, "H")) _
                                And cel.Address <> "$H$14" And cel.Address <> "$H$18") Then
                    Selection.Font.Color = -65536    '超链接字体蓝色
                    Selection.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
                End If
                
            Next cel
        
            Cells(r + 1, 1).Select
            Selection.Font.Size = 8 '对最后的codelist/content 超链接变形式
            Selection.Font.Name = "Times New Roman"
            If Cells(r + 1, 1).HasFormula = True Then
                Selection.Font.Color = -65536
                Selection.Font.Underline = xlUnderlineStyleSingle
            End If
        End If
        
        
        
        If r0 = 2 Then
            i.Rows("2:" & rmax).Select
            Selection.Interior.Pattern = xlNone '把整行填充颜色去掉
            Selection.Font.Name = "宋体"
            Selection.Font.Name = "Times New Roman"
            Selection.Font.Size = 8
            Selection.Font.Underline = xlUnderlineStyleNone '字体下划线
            Selection.Font.ColorIndex = xlAutomatic '字体颜色自动
                        
            Set cell_last = Range("A" & rmax)
                                
            If cell_last.HasFormula = True Then
                
                cell_last.Font.Color = -65536    '超链接字体蓝色
                cell_last.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
            End If
            
            
            '处理ocdelist的CT
            If i.Name = "CODELIST" Then
                'cmax1 = i.Range("Z2").End(xlToLeft).Column
                cmax1 = 6
                Set r2 = Range(Cells(1, cmax1), Cells(rmax, cmax1))
                For Each cel2 In r2
                    If cel2.HasFormula = True Then
                        
                        cel2.Font.Color = -65536    '超链接字体蓝色
                        cel2.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
                    End If
                Next
                
                i.Range(Cells(2, 1), Cells(rmax, cmax1)).Select
                Selection.Borders.LineStyle = xlContinuous '框线
            End If
                
        End If
        
    Next
    
End Sub

```


---

## 2. ADAM Spec检查

```vba
Sub cont()
    Dim r1, r, c, cel, judge, i, r0, cell_last, cmax1, cmax, rmax, r2, cel2
    For Each i In Worksheets
        i.Activate
        rmax = i.Range("A" & Rows.Count).End(xlUp).Row
        cmax = i.Range("Z1").End(xlToLeft).Column
    
        Set judge = i.Range("A2:C11")
        
        If i.Name = "CONTENT" Then  '对于spec的content表里，规范格式
                r = 7
            ElseIf i.Range("A12") = "" And i.Range("A13") <> "" And _
                    Range("A2:C2").MergeCells = True And Range("A3:C3").MergeCells = True And Range("A4:C4").MergeCells = True _
                    And Range("A5:C5").MergeCells = True And Range("A6:C6").MergeCells = True And Range("A7:C7").MergeCells = True _
                    And Range("A8:C8").MergeCells = True And Range("A9:C9").MergeCells = True And Range("A10:C10").MergeCells = True And Range("A11:C11").MergeCells = True _
            Then
                r = 14
            Else: r = 2
                 
        End If
        r0 = r
        
        'MsgBox "表单是：" & i.Name & ",开始行数是：" & r
        c = 1
        Do While i.Range("A" & r) <> ""   '计算多少行，比实际多加1
            Range("A" & r).EntireRow.Select '选中该整行
            Selection.Interior.Pattern = xlNone '把整行填充颜色去掉
            
            If Cells(r, 3) = "数值型" Then Cells(r, 4).Value = 8 '把数据类型为数值型的，赋值长度为8.有的没赋值，为统一，统一赋值
            r = r + 1
        Loop
        
        Do While Cells(r0 - 1, c) <> "" '计算多少列,比实际多加1。
            c = c + 1
            
        Loop
        
        'MsgBox "有效区域是：第" & r & "行，第" & c & "列"
        If r0 <> 2 Then
                Set r1 = Range(Cells(r0, 1), Cells(r - 1, c - 1))  '选中有效的数据
                r1.Select
                Selection.Font.Name = "宋体"
                Selection.Font.Name = "Times New Roman"
                Selection.Font.Size = 8
                Selection.Font.Underline = xlUnderlineStyleNone '字体下划线
                Selection.Borders.LineStyle = xlContinuous '框线
                Selection.Font.ColorIndex = xlAutomatic '字体颜色自动
                 
                
                For Each cel In r1     '遍历每个cell，设置。除超链接，其余字体颜色为自动。超链接字体蓝色，加下划线
                    cel.Select
                    
                    If (r0 = 14 And InStr(i.Name, "SUPP") = 0 And (InStr(cel.Address, "F") Or InStr(cel.Address, "H")) And cel.HasFormula = True _
                        And (cel.Address <> "$H$14" And cel.Address <> "$H$15" And cel.Address <> "$H$16" And cel.Address <> "$H$17")) Or _
                            (i.Name = "CONTENT" And InStr(cel.Address, "$A$")) Then
                        Selection.Font.Color = -65536    '超链接字体蓝色
                        Selection.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
                    End If
                    
                Next cel
            
            
            Cells(r + 1, 1).Select
            Selection.Font.Size = 8 '对最后的codelist/content 超链接变形式
            Selection.Font.Name = "Times New Roman"
            If Cells(r + 1, 1).HasFormula = True Then
                Selection.Font.Color = -65536
                Selection.Font.Underline = xlUnderlineStyleSingle
            End If
        End If
        
        
        If r0 = 2 Then
            i.Rows("2:" & rmax).Select
            Selection.Interior.Pattern = xlNone '把整行填充颜色去掉
            Selection.Font.Name = "宋体"
            Selection.Font.Name = "Times New Roman"
            Selection.Font.Size = 8
            Selection.Font.Underline = xlUnderlineStyleNone '字体下划线
            Selection.Font.ColorIndex = xlAutomatic '字体颜色自动
                        
            Set cell_last = Range("A" & rmax)
                                
            If cell_last.HasFormula = True Then
                
                cell_last.Font.Color = -65536    '超链接字体蓝色
                cell_last.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
            End If
            
            
            '处理ocdelist的CT
            If i.Name = "CODELIST" Then
                'cmax1 = i.Range("Z2").End(xlToLeft).Column
                cmax1 = 6
                Set r2 = Range(Cells(1, cmax1), Cells(rmax, cmax1))
                For Each cel2 In r2
                    If cel2.HasFormula = True Then
                        
                        cel2.Font.Color = -65536    '超链接字体蓝色
                        cel2.Font.Underline = xlUnderlineStyleSingle '超链接加下划线
                    End If
                Next
                
                i.Range(Cells(2, 1), Cells(rmax, cmax1)).Select
                Selection.Borders.LineStyle = xlContinuous '框线
            End If
                
        End If
        
    Next
    
End Sub

```
